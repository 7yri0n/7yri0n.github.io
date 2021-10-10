---
title: Docker Linux 
---

## Docker

Encapsulación de aplicaciones con bibliotecas, configuración, etcetera. De esta manera, tenemos una aplicación portable y flexible. <span style="color:red">Cualquier software en cualquier hardware.</span>

- Aislamos enternos (+ seguro)
- Uso de recursos del S.O. host compartiendo entre dockers.

![Docker esquema](/theme/img/imagenesDocumentation/docker_esquema.jpeg)

## Docker images

Un docker images es un template de un entorno el cual ya tiene todos los elementos necesarios para la app (libs, config files, software, code, ...)

Se pueden crear diferentes versiones de la imagen.

Estas imagenes se crean usando un Dockerfile que contiene la configuración para construir la imagen (instrucciones)

## Comandos Docker

Descargar una imagen Docker:

`docker pull docker_imagen`

Ejecutar un nuevo container apartir de una imagen Docker:

`docker run [OPTIONS] docker_imagen [COMMAND]` 
`docker run -it ubuntu bash` (Ejecución del comando bash sobre la imagen docker Ubuntu)

Con la opción **-i**(interactive) mantiene una entrada abierta para interactuar con el container.
Con la opción **-t** se asigna una pseudo-TTY.
Con la opción **-d** se ejecuta el contenedor en segundo plano.

`docker run -d docker_imagen`

Listar que containers se están ejecutandose: 

`docker ps -a`

Un dockerfile se puede estar ejecutando varias veces.

Re-iniciar un container Docker:

`docker start container_id`

Parar un container:

`docker stop container_id`

`docker stop $(docker ps -aq)` (Parar todos los contenedores)

Eliminar un container Docker:

`docker rm container_id`

Eliminar la imagen total del docker:

`docker rmi container_imagen`

Entrar en un docker en ejecución:

`docker attack docker_id`

Para salirse de un contenedor sin parar de ejecutarlo: Ctrl + P -> Ctrl + Q

Guardar un estado de contenedor de Docker sobre una nueva imagen Docker:

`docker commit docker_id new_name_imagen`

Enlazar el puerto del container Docker con uno del host S.O.:

`docker run -p puerto_host:puerto_docker docker_imagen`

Ej: `docker run -p 3000:80 nginx`

Enlazar una carpeta local con una del docker con la opción **-v**:

`docker run -d --name mi_docker --network name_network -p 1234:80 -v /carpeta/host:/carpeta/docker docker_imagen`


## Docker Network

Mostrar las redes predefinidas:

`docker network ls`

Los datos que nos muestra este comando:

- NETWORK ID
- NAME:
	1. Host: El contenedor será accesible usando la misma IP que la máquina host.

	`docker run -d --name docker --network host image`

	2. Brigde: Los contenedores conectados a esta red que quieren exponer algún puerto al exterior tienen que usar la opción -p.
- DRIVER
- SCOPE

Crear una nueva red de tipo brigde:

`docker network create name_network`

Ver características de una red:

`docker network inspect name_network`












