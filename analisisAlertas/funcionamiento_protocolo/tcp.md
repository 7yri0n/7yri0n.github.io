---
title: TCP (Transmission Control Protocol)
---

## TCP (Transmission Control Protocol)

TCP permite establecer una conexión entre dos puntos terminales en una red informática común que posibilite un intercambio mutuo de datos. En este caso, <span style="color:red">cualquier pérdida de datos se detecta y se resuelve.</span>

Transmisión por paquetes limitados a 1500 bytes. Cada conexión se debe identificar siempre claramente mediante dos puntos terminales definidos (cliente y servidor).

Para el establecimiento de una conexión TCP, ambos puntos deben contar con una IP unívoca y habilitado el puerto deseado para la transmisión de datos. Mientras que la dirección IP funciona como característica de identificación, el puerto sirve para que el sistema operativo pueda asignar las conexiones a las aplicaciones de servidor y de cliente.

**Secuencia** (Three way handshake) -> Establecer una conexión TCP.

![Three way handshake](/theme/img/imagenesDocumentation/handshake_tcp.jpg)

1. El cliente que quiere establecer una conexión TCP envía un paquete SYN (sincronizar) con un número de suencia individual y aleatorio. <span style="color: red">(SEQ.Client)</span>
2. El servidor que recibe el segmento, confirma el establecimiento de la conexión mediante el envío de un paquete SYN-ACK (Confirmación) y envia:
	- Número de secuencia de Cliente + 1 <span style="color: red">(SEQ.Client + 1)</span>
	- Número de secuencia propio de server <span style="color: red">(SEQ.Server)</span>
3. El cliente confirma la recepción del segmento SYN-ACK mediante el envio de un paquete ACK propio con:
	- Número de secuencia de Cliente + 1 <span style="color: red">(SEQ.Client + 1)</span>
	- Número de secuencia de server + 1 <span style="color: red">(SEQ.Server + 1)</span>

**Termination** (TCP teardown) -> Fin de conexión TCP en cuatro pasos.

Ambos terminales pueden terminar una conexión TCP establecida, inclusivo de manera unilateral (conexión semicerrada).

![TCP teardown]](/theme/img/imagenesDocumentation/termination_tcp.jpg)

1. El cliente envía un paquete FIN al servidor para comunicarle que ya no desea más datos. (envia un número de sec. propio)
2. El servidor confirma la recepción del paquete mediante un paquete ACK que incluye el envío del número de secuencia del cliente + 1.
3. Si el servidor finalizó la transmisión de datos, envía un paquete FIN al cliente con el número de secuencia propio del servidor.
4. Ahora el cliente envía el paquete ACK para confirmar con el número de secuencia del servidor + 1.


