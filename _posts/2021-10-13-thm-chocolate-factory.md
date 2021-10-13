---
title:  CTF TryHackme Chocolate Factory
tags:
  - Stego
  - Privilege escalation
  - Tryhackme
  - Linux
---

<div class="card mb-3">
    <center>
            <img class="card-img-top" src="/theme/img/THM/Chocolate Factory/titulo_chocolate_factory.jpeg"/>
    </center>
</div>

Máquina: Linux

IP: 10.10.85.4

Creada por AndyInfosec

<!--more-->

## User flag

Se realiza un reconocimiento para ver qué puertos tiene abiertos la máquina usando **nmap**:

```bash
nmap -p- 10.10.85.4
```

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/nmap.png"/>
    </center>
</div>

Estos puertos abiertos pueden ser falsos y no tener ningún servicio alojado en ellos, así que para ver que tienen dentro se realiza un escaneo más profundo:

```bash
nmap -p105,22,112,101,106,122,104,107,109,110,108,120,115,117,119,116,121,80,114,123,21,125,102,100,113,124,111,118,103 -sV -sC -T4 -Pn -oA 10.10.85.4 10.10.85.4
```

La mayoria muestra un texto como el siguiente:

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/mensaje_nmap.png"/>
    </center>
</div>

La máquina tiene los puertos abiertos 21,22,80 y además en el puerto 113, nos indica que encontraremos la clave en **http://localhost/key_rev_key**.

El servicio FTP tiene activado el usuario anonymous/anonymous, por lo que al entrar, se obtiene una foto **gum_room.jpg** con get.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/imagen_stego.png"/>
    </center>
</div>

La imagen a simple vista no nos dice nada. Se prueba con varios programas de Stego (exiftool, binwalk, strings,..) en busca de algo oculto en la foto. Con **steghide** se puede ver que han ocultado algo ya que pide una contraseña para obtenerlo.

Con **steg_brute.py** se crackea la contraseña:

```bash
./steg_brute.py -b -d /usr/share/wordlist/rockyou.txt -f gum_room.jpg
```

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/steg_brute.png"/>
    </center>
</div>

Se obtendrá el fichero /etc/passwd del sistema en base64. El hash de Charlie en SHA512:

**charlie:$6$CZJnCPeQWp9/jpNx$khGlFdICJnr8R3JC/jTR2r7DrbFLp8zq8469d3c0.zuKN4se
61FObwWGxcHZqO2RJHkkL1jjPYeeGyIJWE82X/:18535:0:99999:7:::**

Se utiliza hashcat y el diccionario rockyou.txt para crackearlo:

```bash
sudo ./hashcat -m 1800 -o output.txt hash.txt /usr/share/wordlist/rockyou.txt --force
```

Se obtiene la contraseña de Charlie.

En el servicio HTTP, se encuentra una página con un login.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/login.png"/>
    </center>
</div>

Como se ha visto por el puerto 113 nos dijo que la clave es **/key_rev_key**. Se busca en el path de la web y se descarga un binario. El binario pode un usuario, el cual no sabemos, por lo que se puede crackearlo con IDA o simplemente si se tira un **strings** contra el binario y se consigue el usuario y la clave:

**laksdhfas**

**b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='**

Esto de momento no nos sirve para nada así que se deja a parte.

En cuanto al login, se prueba que las crendenciales de Charlie del sistema encajan en el login de la página. Una vez logueado, situados en home.php, vemos lo siguiente:

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/command.png"/>
    </center>
</div>

Es un TextEdit que ejecuta el comando que escribas por terminal, primero se intento a realizar un netcat, pero no hubo suerte. La otra cosa que funcionó, fue buscar la clave privada para conectarse por SSH y que se encontraba en el home de Charlie.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/rsa.png"/>
    </center>
</div>

Con la clave privada ya conectamos al sistema por SSH y la flag de User se encuentra en /home/charlie.

## Root flag

Para elevar privilegios a root, se ejecuta **sudo -l** para ver que comandos se pueden ejecutar como sudo. En este caso, se puede ejecutar **sudo /usr/bin/vi**

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/sudo.png"/>
    </center>
</div>

Desde modo comando del vi, se escribe:

```bash
:set shell=/bin/sh

:shell
```

Con esto, se obtendrá una shell de root a través del comando vi.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/id.png"/>
    </center>
</div>


En el home de root, se encuentra un ejecutable en Python, al ejecutarlo, pide una clave. La clave es la obtenida en el binario key_rev_key. Se inserta la clave y mostrará un banner que al final de él, estará la flag de root.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/THM/Chocolate Factory/banner.png"/>
    </center>
</div>


## Bibliografía

[Uso de Steghide](https://fwhibbit.es/steghide-brute-force-tool)