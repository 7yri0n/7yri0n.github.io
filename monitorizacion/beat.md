---
title: Beats
--- 

## Filebeat

Recoge y envia datos de logs personalizados.

- Fichero de configuración: filebeat.yml

Filebeat está atendiendo a los inodos de los fichero logs. Para cada entrada, Filebeat mantiene un estado de cada archivo que encuentra. Como los archivos pueden ser renombrados o movidos, el nombre del archivo y la ruta no son suficientes para identificar un archivo, asi que almacena los inodos para detectar si un archivo fue recolectado previamente. 

Filebeat lleva en estos registros de por donde está leyendo un fichero. Ej: Está leyendo la linea 7, por lo que esperará a la linea 8,9,..

Estos registros se encuentran en **/data/registry**. Si se quiere volver a leer todos los ficheros, borramos dicho fichero o si solo es uno en concreto, se modifica la linea en concreto.

Para configurar los **paths** de donde leera los ficheros:

```yml
paths:
- ../logs/logs-*.log
```

Si no se quieren enviar ciertas lineas del log, como pueden ser mensajes de DEBUG o otro tipo. Podemos excluir lineas seǵun un patrón regex:

```yml
exclude_lines: ['^DEBUG']
```

Para ver los modulos Filebeat:

```bash
./filebeat modules list 
```

Para activar un módulo en Filebeat:

```bash
./filebeat modules list 
```

Con la opción **loadbalance: true** en el fichero filebeat.yml, se realiza un envio de logs a varios logstash a modo de cluster utilizando Round Robin (uno a cada uno).

```yml
loadbalance: true
hosts: [ "logstash1:5044", "logstash2:5044" ]
```

Opciones de hilos y multitareas con Filebeat. Estos campos se deben minimizar porque Filebeat suele estar en un equipo de producción.

- **pipelining**: Hilos de tramas de manera simultaneamente.
- **workers**: Hilos

```yml
pipelining: 2
workers: 4
```

#### Eventos multilineas

Filebeat lee las lineas de cada fichero, pero no reconoce si existen eventos que consten de varias lineas. Usamos la configuración de multiline para indicar cuando empieza un evento y hasta que linea termina.

- **multiline.pattern**: Patrón indicando que todas las lineas que empiezan por DEBUG o INFO no son nuevas lineas
- **multiline.negate**: true or false. Indica que el patrón debe ser negado.
- **multiline.match**: after or before. Indica a que evento se deben unir esas lineas sueltas al de arriba (before) o al de abajo (after)

```yml
multiline.pattern: ^(DEBUG | INFO).*
multiline.negate: true
multiline.match: after
```

## Metricbeat

Recolecta metricas de los sistemas y servicios. Como CPU, memoria, disco, tráfico entrante y saliente.

- Fichero de configuración: metricbeat.yml
- Input: Se configura a través de los módulos.
- Output: Elasticsearch, Logstash o colas externas (Kafka, Redis)

Para ver los módulos Metricbeat:

```bash
./metricbeat modules list 
```

Para activar un módulo en metricbeat:

```bash
./metribeat modules enable [module]
```

Ejemplo de configuración de fichero system.yml:

```yml
- module: system
  period: 1m         #Cada minuto refresca estas metricas
  metricsets:        #Metricas disponibles en la documentación del módulo
    - filesystem
    - fsstat
    - cpu
    - memory
    - network
    - proccess
```

Comprobar si el fichero conf es correcto:

```bash
./metricbeat test config -c metricbeat.yml
```

Arrancar:

```bash
./metribeat -c metricbeat.yml
```

Una mejora de perfomance en caso de recibir mucha información con metricbeat, es gestionando las colas de metricbeat. 

- **queue_mem.events**: eventos que caben en la cola
- **queue_mem.flush_min_events**: Número de eventos minimos enviados a elasticsearch. Envio de eventos en paquetes de 512.
- **queue_mem.flush_timeout**: En caso de que la cola no llegue a 512 eventos, se enviará si pasan 5 seg.

```yml
queue_mem:
  events: 2000
  flush_min_events: 512
  flush_timeout: 5s
```

## Packetbeat

Monitorizar el tráfico de red que pasa por un equipo tanto a nivel de app como de red.

- Fichero de configuración: packetbeat.yml

Comprobar que interfaces detecta packetbeat:

```bash
./packetbeat devices
```

Arrancar:

```bash
./packetbeat -c packetbeat.yml
```

En caso de que solo queramos escuchar el tráfico de una interfaz o de todas, se configura el fichero packetbeat.yml:

```yml
packetbeat.interface.device: any o [ens38]
```

Se puede configurar a nivel de procesos o puertos.

## Heartbeat

Conocer el estado de los servicios (caidas).

- Fichero de configuración: heartbeat.yml

Ejemplo de chequeo de portales via HTTP

```yml
hearbeat.monitors:
- type: http
  urls: [
       "http://localhost:5601",
       "http://miportal.es"
  ]
  schedule: '*/5 * * * * * *' #Comprueba cada 5 seg
  check.response.status: 200 #La respuesta que se desea esperar
```

Otro ejemplo serían peticiones ICMP o TCP contra equipos o URLs.

Para comprobar los datos => Kibana > Uptime

Arrancar:

```bash
./heartbeat -c heartbeat.yml
```

## Otros Beats

- **Auditbeat**: Métricas específicas de cada sistema operativo.
- **Winlogbeat**: Windows logs