---
title: TCP (Transmission Control Protocol)
---

## TCP (Transmission Control Protocol)

TCP permite establecer una conexión entre dos puntos terminales en una red informática común que posibilite un intercambio mutuo de datos. En este caso, <span style="color:red">cualquier pérdida de datos se detecta y se resuelve.</span>

Transmisión por paquetes limitados a 1500 bytes. Cada conexión se debe identificar siempre claramente mediante dos puntos terminales definidos (cliente y servidor).

Para el establecimiento de una conexión TCP, ambos puntos deben contar con una IP unívoca y habilitado el puerto deseado para la transmisión de datos. Mientras que la dirección IP funciona como característica de identificación, el puerto sirve para que el sistema operativo pueda asignar las conexiones a las aplicaciones de servidor y de cliente.

## Secuencia (Three way handshake)

Establecer una conexión TCP.

<div class="card mb-3">
    <img class="card-img" src="/theme/img/imagenesDocumentation/handshake_tcp.jpg"/>
</div>

1. El cliente que quiere establecer una conexión TCP envía un paquete SYN (sincronizar) con un número de suencia individual y aleatorio. <span style="color: red">(SEQ.Client)</span>
2. El servidor que recibe el segmento, confirma el establecimiento de la conexión mediante el envío de un paquete SYN-ACK (Confirmación) y envia:
	- Número de secuencia de Cliente + 1 <span style="color: red">(SEQ.Client + 1)</span>
	- Número de secuencia propio de server <span style="color: red">(SEQ.Server)</span>
3. El cliente confirma la recepción del segmento SYN-ACK mediante el envio de un paquete ACK propio con:
	- Número de secuencia de Cliente + 1 <span style="color: red">(SEQ.Client + 1)</span>
	- Número de secuencia de server + 1 <span style="color: red">(SEQ.Server + 1)</span>

## Termination (TCP teardown)

Fin de conexión TCP en cuatro pasos.

Ambos terminales pueden terminar una conexión TCP establecida, inclusivo de manera unilateral (conexión semicerrada).

<div class="card mb-3">
    <img class="card-img" src="/theme/img/imagenesDocumentation/termination_tcp.jpg"/>
</div>

1. El cliente envía un paquete FIN al servidor para comunicarle que ya no desea más datos. (envia un número de sec. propio)
2. El servidor confirma la recepción del paquete mediante un paquete ACK que incluye el envío del número de secuencia del cliente + 1.
3. Si el servidor finalizó la transmisión de datos, envía un paquete FIN al cliente con el número de secuencia propio del servidor.
4. Ahora el cliente envía el paquete ACK para confirmar con el número de secuencia del servidor + 1.

## Flags

Hay 6 posibles bits individuales en el campo "flags" del paquete TCP:

- **URG (Urgent)**: Señaliza a la aplicación TCP que se deben procesar inmediatamente.
- **ACK (Confirmación)**: Sirve para confirmar la recepción de paquetes TCP.
- **PSH (Push)**: Facilitar un segmento TCP inmediatamente sin tener que pasar por el buffer de datos del emisor y receptor.
- **RST (Reset)**: Ha surgido un error durante la transmisión, la aplicación puede reestablecerse mediante un paquete TCP con flag RST ajustado.
- **SYN (Sincronizar)**: Representa el primer paso del triple apretón de manos (iniciar el establecimiento de la conexión).
- **FIN (Finish)**: Señaliza el fin de la comunicación.

Para que se puedan enviar paquetes TCP más grandes que 1460 bytes de datos, existe la **segmentación**, en la que los datos de aplicación se dividen en varios bloques antes del transporte, se enumeran y se envían en un orden aleatorio. Como receptor debe confirmar la recepción de cada segmento, los <span style="color: red">puede reconstruir en el orden real mediante los números de secuencia.</span>