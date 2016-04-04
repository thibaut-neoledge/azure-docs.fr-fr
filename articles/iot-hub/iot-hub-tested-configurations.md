<properties
	pageTitle="Compatibilité des plateformes de système d’exploitation | Microsoft Azure"
	description="Résume la compatibilité du Kit de développement logiciel (SDK) IoT d’appareil avec les plateformes de système d’exploitation."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# Compatibilité des plateformes de système d’exploitation avec les Kits de développement logiciel (SDK) d’appareil

Ce document décrit la compatibilité du Kit de développement logiciel (SDK) avec différentes plateformes de système d’exploitation. Si vous ne savez pas quel appareil utiliser, consultez la section sur la compatibilité des [plateformes de système d’exploitation et des bibliothèques](#os-platforms) dans cet article.

## Programme Microsoft Azure Certified pour IoT

Si vous avez déjà un appareil, consultez la liste des appareils inclus dans le [programme Microsoft Azure Certified pour IoT][lnk-certified] pour trouver des informations sur la compatibilité propres à l’appareil. Microsoft Azure Certified for IoT est le programme partenaire qui connecte le vaste écosystème IoT à Microsoft Azure pour que les développeurs et architectes comprennent les scénarios de compatibilité. En particulier, il fournit une liste approuvée de combinaisons de systèmes d’exploitation et d’appareils pour vous aider à implémenter rapidement un projet IoT, que vous soyez dans une phase pilote ou preuve de concept.

## Plateformes de système d’exploitation

Les bibliothèques Azure IoT ont été testées sur les plateformes de système d’exploitation suivantes :


|Plateformes de système d’exploitation Linux/Unix | Version|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Plateformes de système d’exploitation Windows | Version|
|:---------------|:------------:|
|Ordinateurs Windows| 10 |
|Windows IoT Standard| 10 |
|Windows Server| 2012 R2|

|Autres plateformes | Version|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## Bibliothèques C

Le [Kit SDK d’appareils Microsoft Azure IoT pour C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) a été testé sur les configurations suivantes :

|Plateforme de système d’exploitation| Version|Protocoles|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP sur WebSockets |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP sur WebSockets |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP sur WebSockets |
|Ordinateurs Windows| 10 | HTTPS, AMQP, MQTT, AMQP sur WebSockets |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Bibliothèques Node.js

Le [Kit de développement logiciel (SDK) d’appareils Microsoft Azure IoT pour Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) a été testé sur les configurations suivantes :


|Runtime| Version|Protocoles|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP sur WebSockets |



## Bibliothèques Java

Le [Kit de développement logiciel (SDK) d’appareils Microsoft Azure IoT pour Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) a été testé sur les configurations suivantes :

|Runtime| Version|Protocoles|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|

Microsoft Azure IoT service SDK pour Java a été testé sur les configurations suivantes :

|Runtime| Version|Protocoles|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## CSharp

Le [Kit de développement logiciel (SDK) d'appareils Microsoft Azure IoT pour .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) a été testé sur les configurations suivantes :

|Plateforme de système d’exploitation| Version|Protocoles|
|:---------|:----------:|:----------:|
|Ordinateurs Windows| 10 | HTTPS, AMQP, MQTT, AMQP sur WebSockets |
|Windows IoT Standard|10 | HTTPS |

Le code d’agent géré nécessite Microsoft .NET Framework 4.5


## Étapes suivantes

- En savoir plus sur le [programme Microsoft Azure Certified pour IoT][lnk-certified].
- Découvrez plus d’informations sur le développement de solutions à l’aide des [appareils certifiés pour IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->