---
title: ICMP (Internet Control Message Protocol)
---

## ICMP (Internet Control Message Protocol)

Analiza varios nodos de internet (ping/traceroute)

- REQUEST
- REPLY

Los paquetes ICMP tienen un <span style="color: red">code</span> y <span style="color: red">type</span>.

**Type y Code**

1. Type: 0
	- Code 0: **Echo Reply** (Mensaje de respuesta a un Echo Request)
2. Type: 3 (Destination Ureachable: Función que transporta un mensaje generado por un enrutador)
	- Code 0: **Network Unreachable**
	- Code 1: **Host unreachable**
	- Code 2: **Protocol unreachable**
	- Code 3: **Port unreachable**
	- Code 4: **Fragmentation needed**
	- Code 5: **Source route failed**
	- Code 6: **Destination network unknown**
	- Code 7: **Destination network unknown**
	- Code 8: **Military Use**
	- Code 9: **The destination network is administratively prohibited**
	- Code 10: **The destination host is administratively prohibited**
	- Code 11: **The network is unreachable for type of service**
	- Code 12: **The host is unreachable for type of service**
3. Type: 5 **Redirecciones** (Solicita que los paquetes de datos se envían en una ruta alternativa)
	- Code 0: **Redirección de la red**
	- Code 1: **Redirección para el host**
	- Code 2: **Redirección del Tipo de Servicio y de Red**
	- Code 3: **Redirección del Tipo de Servicio y de Host**
4. Type 8:
	- Code 0: **Echo Request** (Mensaje de control que se envía a un host con la expectativa de recibir un Echo Reply)
5. Type 11:
	- Code 0: **Tiempo excedido en el tránsito**
	- Code 1: **Tiempo excedido en el fragmento reensamblaje**
6. Type 13:
	- Code 0: **Timestamp** (Sincronización de tiempo)
7. Type 14:
	- Code 0: **Respuesta Timestamp** (Respuesta)


## Ping (ICMP) Flood

Un ping flood es un ataque DDoS donde el atacante intenta inundar un dispositivo con paquetes ICMP (request reply). Este ataque se realiza desde varios dispositivos a uno concreto (botnet). El daño de este ataque se encuentra proporcional con el número de peticiones realizadas contra el objetivo. El tráfico de ataque de Ping Flood es semétrico, la cantidad de ancho de banda que recibe el dispositivo es simplemente la suma del tráfico total enviado desde cada host.

Se puede mitigar deshabilitando la funcionalidad de ICMP, aunque esto es malo si el administrador quiere hacer uso de las herramientas de network.




