---
title: Instalación Stack ELK en Docker
---

<hr>

## ¿Que es ELK Stack?

- Elasticsearch es un motor de búsqueda y analítica.
- Logstash es un pipeline de procesamiento de datos del lado del servidor que ingesta datos de una multitud de fuentes simultáneamente, los transforma y luego los envía a Elasticsearch.
- Kibana permite a los usuarios visualizar los datos en cuadros y gráficos con Elasticsearch.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/imagenesDocumentation/ELK.png"/>
    </center>
</div>


<br>

## Instalación de ELK Stack con Docker

<hr>

#### Beats

Beats es una plataforma para agentes de datos encargado de enviar datos de cientos o miles de máquinas y sistemas a Logstash o Elasticsearch. Algunos tipos:

- **Filebeat**: Archivos de log
- **Metricbeat**: Métricas
- **Packetbeat**: Datos de red
- **Winlogbeat**: Logs de eventos de Windows
- **Heartbeat**: Monitoreo de tiempo de actividad

Descarga de Beats: [Download](https://www.elastic.co/es/downloads/beats/)

Los ficheros de configuración: filebeat.yml, metricbeat.yml,..

Para mostrar el output de los datos recogidos por consola, configuramos output.console:true.

Ejecución de metricbeat con -c [configuración]
```bash
./metricbeat -c metribeat.yml
```

En un caso real, se configura la entrada **enabled:true** y la ruta de donde se recogeran los logs. **(paths:)**. Se deben definir las salidas **elasticsearch.output** o **logstash.output**

<hr>

#### Logstash

Descarga de Logstash: [Download](https://www.elastic.co/downloads/logstash)

Carpeta de configuración de Logstash (/config)
Ejemplo de pipeline de logstash por stdin:

```bash
input {
	stdin {}
}

output {
	stdout {
		codec => json_lines
	}
}
```

Ejemplo de pipeline de logstash para Beats y Elasticsearch:

```bash
input {
	beats {
		port => 5044 #Puerto donde está escuchando Logstash y en filebeat.yml ya se configuro como salida
	}
}

output {
	elasticsearch {
		hosts => "localhost:9200"
	}
}
```

Si logstash no detecta ninguna configuración (pipeline) se parará.

Ejecución de logstash con pipeline:
```bash
./logstash -f pipelines.conf
```

<hr>

#### Elasticsearch

Descarga de Elasticsearch: [Download](https://www.elastic.co/downloads/elasticsearch)

Ejecución de elasticsearch:
```bash
./elasticsearch -d 
```

Para comprobar que esta arrancado ejecutamos un *curl localhost:9200*

<hr>

#### Kibana

Descarga de Kibana: [Download](https://www.elastic.co/downloads/kibana)

```yml
elasticsearch.url: "http://localhost:9200"
server.host: "0.0.0.0"
```

La configuración **elasticsearch.url: "localhost:9200"** permite a Kibana buscar en esa dirección donde se encontraría Elasticsearch y con **server.host: "0.0.0.0"** permite escuchar peticiones desde cualquier sitio de la red (Método para crear restricciones).

Ejecución de Kibana
```bash
./kibana 
```

<hr>

#### Docker

Arrancar el stack ELK en docker: [docker.elastic.co](https://docker.elastic.co/)

Con uso del siguiente script docker-compose:

```yml
version: '3.7'
services:
 elasticsearch:
  volumes:
   - ./elasticsearch/data:/usr/share/elasticsearch/data
  environment:
   - bootstrap.memory_lock=true
   - "ES_JAVA_OPTS= -Xms512m -Xmx512m"
   - discovery.type=single-node
  image: docker.elastic.co/elasticsearch/elasticsearch:7.15.2
  ports:
   - 9200:9200
   - 9300:9300
  ulimits:
   memlock:
    soft: -1
    hard: -1

 logstash:
  image: docker.elastic.co/logstash/logstash:7.15.2
  volumes:
   - ./logstash/pipeline:/usr/share/logstash/pipeline

 kibana:
  image: docker.elastic.co/kibana/kibana:7.15.2
  ports:
   - 5601:5601
```

Para monitorizar un servicio (elasticsearch,kibana,logstash) del docker-compose:

```bash
docker-compose logs -f service
```

Si logstash no detecta ninguna configuración (pipeline) se parará.

Para ejecutar el script de docker-compose:

```bash
docker-compose exec elasticsearch /bin/bash
```
<br>

## Puesta en marcha

El orden de arrancado de los servicios es importante:

1. Elasticseach
2. Kibana
3. Logstash
3. Beats

<br>

## Troubleshooting

<hr>

#### Errores en Elasticsearch

*ElasticsearchException[failed to bind service]; nested: AccessDeniedException[/usr/share/elasticsearch/data/nodes];*

Se trata de un error de permisos a la hora de mapear un volumen en el docker-compose o docker. Para arreglarlo damos permisos totales a la carpeta mapeada en el host y eliminamos cualquier contenido que pueda haber en esa carpeta.

```bash
chmod 777 ./elasticsearch/data
rm -R ./elasticsearch/data/*
```