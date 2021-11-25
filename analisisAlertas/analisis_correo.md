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


## Análisis de cabeceras de correo






- Determinar la IP origen del emisor.

	¿Es realmente la IP origen?¿El origen es el servidor SMTP?







https://dvirus.training/2019/12/11/analisis-de-correos-maliciosos/#Preparacion