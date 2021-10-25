---
title: Logs de Paloalto
---

Paloalto ofrece diversos tipos de logs:

- Traffic Logs
- Threat Logs
- URL Filtering Logs
- WildFire Submissions Logs
- Correlation Logs
- Tunnel Inspection Logs
- Config Logs
- System Logs
- HIP Match Logs
- IP-Tag Logs
- User-ID Logs

## Traffic Logs en firewall Paloalto [1]

Traffic: Muestra una entrada del comienzo y fin de cada sesión/conexión. Información relevante: Hora y fecha, IP y puerto de origen y destino, nombre de aplicación, regla que se aplica, acción (allow, deny o drop), número de bytes y razón de fin de sesión. Los atributos *FUTURE_USE* no aparecen en la lista. 

~~~
"2021/10/19 12:23:27,013201020597,TRAFFIC,drop,FUTURE_USE,2021/10/19 12:23:26,
172.26.13.20,192.168.28.248,0.0.0.0,0.0.0.0,interzone-default,UsuarioOrigen,
UsuarioDestino,not-applicable,vsys1,FromZone,ToZone,ae4.2613,oi.352,LOG_Panorama,
2021/10/19 12:23:26,0,1,38676,30005,NatSrcPort,NatDstPort,0x19,tcp,deny,78,78,0,1,2021/10/19 12:23:27,0,
any,FUTURE_USE,6998109464283891297,0x8000000000000000,172.16.0.0-172.31.255.255,
192.168.0.0-192.168.255.255,FUTURE_USE,1,0,policy-deny,11,0,0,0,nameVirtual,PA01_5220,from-policy,,,0,,0,,
N/A,0,0,0,0,39e5b85a-956a-4e18-aab8-6256180db4f2,0,0,,,,,,,,0.0.0.0,,,,,,,,,,,,
,,,,,,,,,,,,,,,2021-10-19T12:23:27.429+02:00,,"
~~~


1. **Receive Time (2021/10/19 12:23:27)**: Fecha de llega del log al management de Paloalto.
2. **Serial number (013201020597)**.
3. **Type (TRAFFIC)**: tipo de Log (TRAFFIC, THREAT, URL Filtering, ...)
4. **<span style="color: red">Subtype</span> (drop)**: Especifica un subtipo (**start**: sesión empieza, **end**: sesión termina, **drop**: sesión es dropeada después de que se identifica la aplicación y no hay regla que permita esta sesión (política implícita) y **deny**: sesión es denegada antes de identificar la aplicación y hay una regla que bloquea este tráfico (política explícito))
5. **Generated Time (2021/10/19 12:23:26)**: Fecha que se genera el log en el dataplane.
6. **Source Address (172.26.13.20)**
7. **Destination IP (192.168.28.48)**
8. **NAT Source (0.0.0.0)**: En caso de que se aplique NAT sobre la IP origen
9. **NAT Destination (0.0.0.0)**: En caso de que se aplique NAT sobre la IP destino
10. **Rule Name (interzone-default)**: Nombre de la regla matcheada
11. **Source User (UsuarioOrigen)**
12. **Destination User (UsuarioDestino)**
13. **<span style="color:red">Application</span> (not-applicable) [2]**: Aplicación asociada a la conexión.
	- incomplete [3]: El TCP handshake no se completó con éxito, ya sea porque el paquete nunca llegó o porque llegó fuera de la ventana de tiempo de espera. (TCP)
	- insufficient data: No hay información suficiente para identificar la aplicación, aunque si se completó el TCP handshake y hubo envió de algún paquete a mayores.
	- unknown-tcp: Hay tráfico TCP desconocido, pero el firewall no ha podido determinar la aplicación (custom application).
	- unknown-udp: Hay tráfico UDP desconocido.
	- unknown-p2p: Tráfico Peer-to-Peer desconocido.
	- not-applicable: Paloalto ha recibido datos que se descartaŕan porque el puerto o servicio por el que entra el tráfico no está permitido o no hay ninguna regla o políotica que permita ese puerto o servicio.
14. **Virtual system (vsys1).**
15. **Source Zone (FromZone)**: Zona origen.
16. **Destination Zone (ToZone)**: Zona destino.
17. **Inbound Interface (ae4.2613)**: Interfaz origen
18. **Outbound Interface (oi.352)**: Interfaz destino
19. **Log Action (LOG_Panorama)**: Log registro que se aplico en la conexión.
20. **Session ID (0)**: Identificador númerico interno que se aplica a cada sesión.
21. **<span style="color:red">Repeat Count</span> (1)**: Número de sesiones con la misma IP origen, destino, application y subtipo visto en el intervalo de 5 seg.
22. **Source Port (38676)**
23. **Destination Port (30005)**
24. **NAT Source Port (NatSrcPort)**
25. **NAT Destination Port (NatDstPort)**
26. **<span style="color: red">Flags [4]</span> (0x19)**: Ofrece información interesante si se aplica una operación AND entre el valor flag (0x19 en este caso) y los <a href="#header2">valores la lista Flags</a>:
	- Si el resultado de ambos es > 0, se aplica.
	- Si el resultado de ambos es = 0, no se aplica
27. **IP protocol (tcp)**: tcp/udp
28. **<span style="color: red">Action [5][6]</span> (deny)**:
	- allow: conexión permitida por política.
	- deny: conexión denegada por política.
	- drop: conexión denegada de forma implicita.
	- reset-both: La conexión es terminada y TCP reset es enviado a ambos lados de la conexión. Posiblemente debido a que el firewall detectó una conexión maliciosa y la bloqueó.
	- reset-client: La conexión es terminada y TCP reset es enviado hacia el cliente. Es útil cuando la experiencia del usuario es clave en el uso de la aplicación. El usuario sabrá inmediatamente que la conexión no está disponible.
	- reset-server: La conexión es termianda y TCP reset es enviado hacia el servidor. Es útil cuando los recursos internos necesitan ser protegidos del consumo excesivo de recursos debido a sockets medio abiertos.
29. **Bytes (78)**: Número total de bytes enviados y recibidos.
30. **Bytes Sent (78)**: Número total de bytes enviados.
31. **Bytes Received (0)**: Número total de bytes recibidos.
32. **Packets (1)**: Número de paquetes transmitidos en la sesión.
33. **Start time (2021/10/19 12:23:27)**: Tiempo cuando empieza la sesión.
34. **<span style="color: red">Elapsed Time</span> (0)**: Tiempo transcurrido en la sesión.
35. **Category (any)**: Categoria de la URL asociada a la sesión.
36. **Sequence Number (6998109464283891297)**: Número unico de cada log.
37. **Action Flags (0x8000000000000000)**: Un campo de bits que indica si el registro se reenvió a Panorama (0x8 es que si).
38. **Source Country (172.16.0.0-172.31.255.255)**: Subred de la IP origen
39. **Destination Country (192.168.0.0-192.168.255.255)**: Subred de la IP destino
40. **Paquetes enviados (1)**: Número de paquetes enviados client-to-server
41. **Paquetes recibidos (0)**: Número de paquetes recibidos server-to-client
42. **<span style="color: red">Session End Reason</span> (policy-deny)**: Razón por la cual se terminó la sesión. En caso, de dos razón se pondrá la que tenga mayor prioridad (ordenadas):
	- threat: El firewall detecta una amenaza asociada con un reset, drop o block IP action.
	- policy-deny: La sesión coincide con una regla que su acción es denegar o dropear.
	- tcp-rst-from-client: El cliente envia un TCP reset al servidor
	- tcp-rst-from-server: El servidor envia un TCP reset al cliente.
	- resources-unavailable: La sesión es dropeada porque limitación de recursos del sistema. Ej: Sesión supero el número de paquetes Out-of-order permitidos en una sesión o global en una cola.
	- tcp-fin: Ambas hosts terminan la conexión con TCP FIN.
	- tcp-reuse: Una sesión es reusada y el firewall cierra la sesión anterior.
	- decoder: El decodificador detecta una nueva conexión dentro del protocolo (como HTTP-Proxy) y termina la conexión anterior.
	- aged-out: La sesión se ha terminado.
	- unknown: Sesión terminada precedida de una razón no contemplada.
	- n/a: Se aplica cuando el subtipo de traffic log no es end.
43. **Device Group Hierarchy level 1,2,3,4 (11,0,0,0)**: Secuencia de números de identificación que indican la ubicación del grupo de dispositivos dentro de una jerarquía de grupos de dispositivos.
44. **Virtual System Name (nameVirtual)**: Nombre del sistema virtual asociada a la sesión.
45. **Device Name (PA01_5220)**: Hostname firewall asociado a la sesión.
46. **Action Source (from-policy)**: La acción que se tomó (allow or block) viene definida en la aplicación o en la política del firewall.
47. **Source VM UUID (src_uuid), Destination VM UUID (dst_uuid), Tunnel ID/IMSI (tunnelid/imsi), Monitor Tag/IMEI (monitortag/imei), Parent Session ID (parent_session_id), Parent Start Time (parent_start_time), Tunnel Type (tunnel), SCTP Association ID (assoc_id), SCTP Chunks (chunks), SCTP Chunks Sent (chunks_sent) y SCTP Chunks Received (chunks_received)**


## Cosas interesantes

Las acciones reset-* son paquetes RST inyectados en tráficos TCP, por lo que si que hay conexión.

Importante diferenciar entre la action del firewall y la Sessión End Reason. La acción del firewall es lo que ha sucedido con la sesión y la razón de porque se hizo es la session end reason.

## Lista Flags Logs Traffic 

- **0x80000000** session has a packet capture (PCAP)
- **0x40000000** option is enabled to allow a client to use multiple paths to connect to a destination host
- **0x20000000** file is submitted to WildFire for a verdict
- **0x10000000** enterprise credential submission by end user detected
- **0x08000000** source for the flow is an allow list and not subject to recon protection
- **0x02000000** IPv6 session
- **0x01000000** SSL session is decrypted (SSL Proxy)
- **0x00800000** session is denied via URL filtering
- **0x00400000** session has a NAT translation performed
- **0x00200000** user information for the session was captured through Captive Portal
- **0x00100000** application traffic is on a non-standard destination port
- **0x00080000** X-Forwarded-For value from a proxy is in the source user field
- **0x00040000** log corresponds to a transaction within a http proxy session (Proxy Transaction)
- **0x00020000** Client to Server flow is subject to policy based forwarding
- **0x00010000** Server to Client flow is subject to policy based forwarding
- **0x00008000** session is a container page access (Container Page)
- **0x00002000** session has a temporary match on a rule for implicit application dependency handling. Available in PAN-OS 5.0.0 and above.
- **0x00000800** symmetric return is used to forward traffic for this session
- **0x00000400** decrypted traffic is being sent out clear text through a mirror port
- **0x00000100** payload of the outer tunnel is being inspected


## Bibliografia 

[Bibliografia 1](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/traffic-log-fields)<br>
[Bibliografia 2](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000ClibCAC)<br>
[Bibliografia 3](https://live.paloaltonetworks.com/t5/blogs/discussion-of-the-week-application-incomplete/ba-p/286965)<br>
[Bibliografia 4](https://live.paloaltonetworks.com/t5/general-topics/flags-field-in-csv-file/td-p/224525)<br>
[Bibliografia 5](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000ClTaCAK)<br>
[Bibliografia 6](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-web-interface-help/objects/objects-security-profiles/actions-in-security-profiles.html)<br>

