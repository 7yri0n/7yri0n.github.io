---
title: Análisis de correo maliciosos
---

A continuación, se describen unos pasos para analizar un incidente relacionado con correos siguiendo una metodologia.

<hr>

## Clasificación de correo

Para empezar es necesario clasificar el correo reportado, si se trata de un correo de spam, phishing, extorsión, con contenido adjunto malicioso o simplemente es un correo legítimo, aunque para clasificarlo antes aqui se debe analizar el correo.

1. Spam o correo no deseado:
	- No tiene contenido malicioso
	- Es enviado a modo broadcast a todas las cuentas de un dominio sin un usuario objetivo.
	- **Motivo**: Publicidad o Anuncios
2. Phishing:
	- Contiene un link adjunto o un .html adjunto suplantando la identidad de una página.
	- Puede enviarse de manera general o contra un usuario objetivo.
	- **Motivo**: Obtención de datos sensibles
3. Extorsión:
	- Mensaje de extorsión para realizar un pago por el rescate de supuesta información comprometida.
	- Suele venir acompañado de cuentas bancarias/monederos bitcoins para enviar el dinero solicitado.
	- **Motivo**: Obtener dinero.
4. Contenido adjunto malicioso:
	- Correo con adjuntos maliciosos (.exe, .bin, .dll, .docx, ...) y URLs con código malicioso 
	- **Motivo**: Comprometer el equipo o ganar el acceso a él.
5. Legítimo o Falso positivo:
	- Correo sospechoso que ha sido considerado malicioso al principio y después del análisis es legítimo.

<hr>

## Análisis general de correo

Una vez tengamos identificado el tipo de correo a analizar, es recomendable hacerse las siguientes preguntas y conseguir sus respuestas para conocer el alcance del incidente.

- ¿Este correo contra que usuario o cuentas se ha dirigido? 
- ¿Existen otros correos con modificaciones (nombre de la cuenta, asunto, dominio) que siguen la misma casuística que el correo original?
- ¿La IP origen del correo es la real, es la de un proxy, viene de una IP interna?
- ¿Contenido del email?¿URLs, archivos maliciosos, motivo del correo?
- ¿El emisor de correo es una suplantación de identidad?

<hr>

## Análisis específico de correo

Ahora con una idea general del correo que estamos analizando, tenemos varias herramientas o tecnicas que nos van a ayudar a comprender las preguntas que no hayamos resuelto.

#### Cabeceras de correo

De las cabeceras de un correo, podemos obtener toda la información posible, pero para un análisis las más importantes son las siguientes:

- **Date**: Día, Fecha, Hora y zona horaria del mensaje (Date: Thu, 25 Nov 2021 04:00:45 +0000)
- **Subject**: Asunto del mensaje
- **From**: Emisor o dirección de quien envia el correo 
- **To**: Destinatario del correo.
- **Received**: Muestra una lista de todos los servidores / ordenadores por las que el correo ha viajado hasta llegar al destinatario. **Se leen de abajo hacia arriba**. La primera linea de "Received" es tu propio sistema. La última linea "Received" es donde se originó el correo. Un ejemplo:

	1. from: Dominio y dirección IP desde donde se retransmitio el correo en cualquier punto específico..
	2. by: La IP o nombre del servidor de donde se envio originalmente el mensaje. **IMPORTANTE**
	3. with: El servicio utilizado para el envió (ESMTP o Microsoft SMTP)
	4. for: Correo al que va dirigido o destinatario final.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/imagenesDocumentation/received.png"/>
    </center>
</div>

Si vemos en este ejemplo la IP original del envio es 74.208.5.2 o dominio mrelay.perfora.net.

<br>

#### Registro SPF

El registro SPF (de Sender Policy Framework) es un tipo especial de registro TXT con un formato concreto que permite a los propietarios de un dominio luchar contra la suplantación de identidad. Mediante el uso de registros SPF los dominios pueden declarar desde qué direcciones IP envían correos. 

- Está diseñado para garantizar que el correo que recibes ha salido de un servidor "autorizado".
- La comparación de la IP del remitente con el registro SPF del dominio la ha de hacer el servidor que recibe el correo. Es una comprobación que recae en el destinatario del email, no en el origen.

Una página para comprobar si una IP pasa el registro SPF para cierto dominio:

[Check SPF](https://www.spf-record.de/spf-lookup)

<hr>

#### Registro DKIM

DKIM es otro mecanismo de seguridad frente a la modificación de correos durante el envio. Al usar DKIM, se incluye una firma cifrada (clave privada) en el encabezado de todos los mensajes salientes. Los servidores de correo electrónico que los reciben descifran su encabezado mediante DKIM y verifican que no se hayan modificado tras el envío.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/imagenesDocumentation/dkim.png" title="Funcionamiento DKIM"/>
    </center>
</div>

<hr>

#### Registro DMARC

DMARC es un mecanismo de autenticación de correo electrónico. Amplía el funcionamiento de dos mecanismos de autenticación existentes, SPF y DKIM. Permite al propietario administrativo de un dominio publicar una política en sus registros DNS que indica qué mecanismo (DKIM, SPF o ambos) se emplea durante el envió de los mensajes de correo electrónico; verificar el campo **From:** que se presenta a los usuarios finales.

<div class="card mb-3">
    <center>
            <img class="card-img" src="/theme/img/imagenesDocumentation/dmarc.png" title="Funcionamiento DMARC"/>
    </center>
</div>


## Respuesta ante incidentes (Playbook)

<hr>

1. **Determinar el alcance del incidente**
	- ¿Correo enviado a un único usuario o múltiples usuarios?.
	- Determinar posibles variaciones del correo. Ejemplo:
		- Mismo asunto con diferentes nombres/dominio/IP
		- Mismos adjuntos con diferentes nombres/dominio/IP
	- Determinar que correos fueron puestos en cuarentena y cuales entregados.
	- Acciones realizadas por los usuarios contra las cuentas sospechosas (Contestaciones, descarga de adjuntos, visita a las páginas/URLs adjuntas)
	- Buscar si se trata de una campaña correo conocida.

2. **Analizar el mensaje**
	- Análisis de IP origen, dominio y emisor. 
	- Análisis de registros SPF y DKIM.
	- Análisis de cabeceras.
	- Analizar todos los adjuntos que puede contener el mensaje:
		- URLs:
			- Análisis en [Virus Total](https://www.virustotal.com/gui/home/upload), [Hydrib Analysis](https://www.hybrid-analysis.com/), [Joe Sandbox](https://www.joesandbox.com/)
			- **Metodologia de análisis de páginas profundo**
		- Adjuntos:
			- Análizar hash y extensión
			- En caso de tener muestra, proceder a un **análisis más profundo**.			

3. **Determinar el impacto**
	- Exfiltración de datos (datos sensibles, cuentas, contraseñas)
	- Posibles equipos infectados.
	- Indisponibilidad de servicio


4. **Mitigación y remediaciones**
	- Cuentas afectadas:
		- Cambiar contraseñas de las cuentas afectadas.
		- Reducir el acceso de la cuenta a servicios criticos hasta que la investigación se complete.
		- Reforzar la autenticación multi-factor (MFA)
	- Bloquear actividad basadas en IOC:
		- Bloquear dominio (URLs o dominio del emisor) en firewall, antispam, proxies
		- Otros posibles bloqueos cuando hay muchas variaciones de correo es según su asunto, mensajes que aparezcan en el cuerpo,...
	- Avisar a los usuarios afectados que eliminen el correo y preguntar posibles acciones contra el correo no capturadas o monitorizadas.
	- Escanear los equipos afectados con herramientas antimalware.
	- Monitorizar IOCs en dispositivos de red durante varias semanas (Posible APT instalada)

## Herramientas

<hr>

[**Email Header Analysis**](https://www.iptrackeronline.com/email-header-analysis.php)

<hr>

**Transformar msg to eml**

```bash
sudo apt-get install libemail-outlook-message-perl libemail-sender-perl
msgconver *.msg
```

<hr>

**Extraer los adjuntos de un .eml**

```bash
sudo apt-get install mpack
munpack 1.eml
```

## Bibliografia

[Bibliografia 1](https://github.com/counteractive/incident-response-plan-template/blob/master/playbooks/playbook-phishing.md) <br>
[Bibliografia 2](https://toolbox.googleapps.com/apps/messageheader/) <br>
[Bibliografia 3](https://www.iana.org/assignments/message-headers/message-headers.xhtml) <br>
[Bibliografia 4](https://raiolanetworks.es/blog/registro-spf/#que_es_un_registro_spf) <br>
[Bibliografia 5](https://dvirus.training/2019/12/11/analisis-de-correos-maliciosos/#Preparacion) <br>