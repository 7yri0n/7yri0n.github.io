---
title: Stack ELK
---


## ¿Que es ELK Stack?

- Elasticsearch es un motor de búsqueda y analítica.
- Logstash es un pipeline de procesamiento de datos del lado del servidor que ingesta datos de una multitud de fuentes simultáneamente, los transforma y luego los envía a Elasticsearch.
- Kibana permite a los usuarios visualizar los datos en cuadros y gráficos con Elasticsearch.

[Imagen funcionamiento ELK STACK]


## Instalación de ELK Stack con Docker



## Troubleshooting

#### Errores en Elasticsearch

**ElasticsearchException[failed to bind service]; nested: AccessDeniedException[/usr/share/elasticsearch/data/nodes];**

Se trata de un error de permisos a la hora de mapear un volumen en el docker-compose o docker. Para arreglarlo damos permisos totales a la carpeta mapeada en el host y eliminamos cualquier contenido que pueda haber en esa carpeta.

```bash
chmod 777 ./elasticsearch/data
rm -R ./elasticsearch/data/*
```