---
title: Análisis de correo maliciosos
---

A continuación, se describen unos pasos para analizar un incidente relacionado con correos siguiendo una metodologia.

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

## Análisis general de correo

Una vez tengamos identificado el tipo de correo a analizar, es recomendable hacerse las siguientes preguntas y conseguir sus respuestas para conocer el alcance del incidente.

- ¿Este correo contra que usuario o cuentas se ha dirigido? 
- ¿Existen otros correos con modificaciones (nombre de la cuenta, asunto, dominio) que siguen la misma casuística que el correo original?
- ¿La IP origen del correo es la real, es la de un proxy, viene de una IP interna?
- ¿Contenido del email?¿URLs, archivos maliciosos, motivo del correo?
- ¿El emisor de correo es una suplantación de identidad?


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

![Ejemplo Received](/theme/img/imagenesDocumentation/received.png)

Si vemos en este ejemplo la IP original del envio es 74.208.5.2 o dominio mrelay.perfora.net.

#### Registros SPF, DMARC, DKIM, MTA, MUA

#### Analizar URL

#### Analizar adjunto

## Mitigación o dispositivos para controlar correos maliciosos

## Respuesta ante incidentes

## Herramientas

https://www.iptrackeronline.com/email-header-analysis.php

**Transformar msg to eml**

```bash
sudo apt-get install libemail-outlook-message-perl libemail-sender-perl
msgconver *.msg
```

## Bibliografia

https://toolbox.googleapps.com/apps/messageheader/
https://www.iana.org/assignments/message-headers/message-headers.xhtml


AnalisisMalware






- Determinar la IP origen del emisor.

	¿Es realmente la IP origen?¿El origen es el servidor SMTP?







https://dvirus.training/2019/12/11/analisis-de-correos-maliciosos/#Preparacion