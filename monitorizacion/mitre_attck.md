---
title: MITRE ATT&CK
---

## ¿Que es MITRE ATT&CK?

Mitre es una corporación/plataforma que organiza y categoriza los distintos tipos de ataques, amenazas y procedimientos realizados por los atacantes.

Recopila información en tiempo real de ataques que categoriza en las siguientes categorias:

MITRE se centra mayoritariamente en detectar cuando un atacante ya ha ganado el acceso (post-compromise).MITRE desarrolló un modelo de comportamiento post-compromise del atacante a modo granular. Este modelo se llama ATT&CK (Adversarial Tactics, Techniques and Common Knowledge). Se usa como módelo de posibles amenazas dentro de la red para que los administradores del sistema puedan defenderse y evaluar la infraestructura.

A mayores, los investigadores de MITRE crearon un método(lista de comportamiento anomalos) para describir los análisis de detección de intrusos por comportamiento y un conjunto de análisis alineados con el módelo ATT&CK. (https://car.mitre.org/analytics/). 

En el módelo ATT&CK, las tacticas describen las metas del atacante al realizar esa acción y las tecnicas describen como se ha realizado poniendo puntos de vista para el Red Team (offensive) y Blueteam (defensive).

## Tactics

Las tacticas representan el nivel más alto de abstracción dentro del módelo ATT&CK. Se podrían catalogar como "metas" de un atacante, una vez este haya ganado el acceso a la red o un equipo comprometido.

- Persistence
- Privilege Escalation
- Defense Evasion
- Credential Access
- Discovery
- Lateral Movement
- Execution
- Collection
- Exfiltration
- Command and Control

## Techniques

Las tecnicas representan que acciones (malware o herramientas) pueden usar los atacantes para alcanzar los objetivos o metas (tacticas) como Persistencia, Defense Evasion, etcetera. Por lo que dentro de cada categoria de tacticas, exiten múltiples tecnicas para alcanzarla.

Una importante distinción entre tecnica en ATT&CK y un IOC, es que muchas tecnicas contienen acciones legitimas realizas por el sistema que también se pueden usar para fines maliciosos, donde un IOC posiblemente indique una brecha de seguridad conocida explotada por un atacante. Es importante, correlar eventos y ponerse en un contexto para diferenciar un falso positivo de una posible amenaza dentro de la red.

**Example ATT&CK Tecnique**

- Behavior: Comportamiento detectado *"Remote execution via scheduled tasks using schtasks.exe"*
- Requirements: Requisitos previos a realizar esta técnica *"Credentials or existing domain permissions providing SMB (Server Message Block –the Windows file sharing mechanism) access to the remote system."*
- Cause: Que sucede al realizar esta tecnica. *"Invocation of schtasks.exeat a command-line interface with arguments to execute a file on a remote system"*
- Effects: Efectos que produce al ejecutarse. *"The schtasks.exeprocess starts on the local system"*

![Matriz MITRE ATT&CK](/theme/img/imagenesDocumentation/ATTACK.png)

