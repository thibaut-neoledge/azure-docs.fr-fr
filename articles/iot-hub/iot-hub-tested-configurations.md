<properties
	pageTitle="Compatibilité des plateformes de système d’exploitation et du matériel Azure IoT | Microsoft Azure"
	description="Compatibilité des plateformes de système d’exploitation et du matériel"
	services="iot-hub"
	documentationCenter="na"
	authors="hegate"
	manager="jamesosb"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="hegate"/>

# Compatibilité des plateformes de système d’exploitation et du matériel

Ce document décrit la compatibilité du Kit de développement logiciel avec différentes plateformes de système d’exploitation, ainsi que les configurations d’appareils spécifiques incluses dans le [programme Microsoft Azure Certified for IoT](#certified). Si vous possédez déjà un appareil, consultez la liste des appareils inclus dans le programme pour trouver des informations sur la compatibilité spécifiques à l’appareil. Si vous ne savez pas quel appareil utiliser, consultez la section sur la compatibilité des [plateformes de système d’exploitation et des bibliothèques](#platforms).


## Plateformes de système d’exploitation

Les bibliothèques Azure IoT ont été testées sur les plateformes de système d’exploitation suivantes :


|Plateformes de système d’exploitation Linux/Unix | Version|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Plateformes de système d’exploitation Windows | Version|
|:---------------|:------------:|
|Ordinateurs Windows| 7,8,10 |
|Windows IoT Standard| 10 |
|Windows Server| 2012 R2|

|Autres plateformes | Version|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## Bibliothèques C

Le [Kit SDK d’appareils Microsoft Azure IoT pour C](c/device/readme.md) a été testé sur les configurations suivantes :

|Plateforme de système d’exploitation| Version|Protocoles|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Ordinateurs Windows| 7,8,10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Bibliothèques Node.js
Le [Kit SDK d’appareils Microsoft Azure IoT pour Node.js](node/device/readme.md) a été testé sur les configurations suivantes :


|Runtime| Version|Protocoles|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Bibliothèques Java
Le [Kit SDK d’appareils Microsoft Azure IoT pour Java](java/device/readme.md) a été testé sur les configurations suivantes :

|Runtime| Version|Protocoles|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

Le [Kit SDK de service Microsoft Azure IoT pour Java](java/service/readme.md) a été testé sur les configurations suivantes :

|Runtime| Version|Protocoles|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp
Le [Kit SDK d’appareils Microsoft Azure IoT pour .NET](csharp/readme.md) a été testé sur les configurations suivantes :

|Plateforme de système d’exploitation| Version|Protocoles|
|:---------|:----------:|:----------:|
|Ordinateurs Windows| 7,8,10 | HTTPS, AMPQ|
|Windows IoT Standard|10 | HTTPS|

Le code d’agent géré nécessite Microsoft .NET Framework 4.5


## Microsoft Azure Certified for IoT

Microsoft Azure Certified for IoT est le programme partenaire qui connecte le vaste écosystème IoT à Microsoft Azure pour que les développeurs et architectes comprennent les scénarios de compatibilité. En particulier, il fournit une liste approuvée de combinaisons de systèmes d’exploitation et d’appareils pour vous aider à implémenter rapidement un projet IoT, que vous soyez dans une phase pilote ou preuve de concept. Avec les combinaisons de systèmes d’exploitation et d’appareils certifiées, votre projet IoT peut démarrer rapidement. Moins de personnalisations et de travail sont nécessaires pour s’assurer que les appareils sont compatibles avec Azure IoT Suite et Azure IoT Hub.


## Appareils certifiés pour IoT

Les appareils certifiés pour IoT ont une compatibilité testée avec les Kits de développement logiciel Azure IoT et sont prêts à être utilisés dans votre application IoT. Plus spécifiquement, nous identifions la compatibilité en fonction de la plateforme de système d’exploitation et du code de langage.

#### Liste d’appareils

 Chaque appareil a été certifié comme fonctionnant avec notre Kit SDK dans le système d’exploitation et le langage choisis par le fabricant de l’appareil. Par exemple, BeagleBone Black fonctionne sur Debian avec notre langage C, Javascript et Java. Cela signifie que les développeurs pourront créer des applications dans n’importe laquelle de ces combinaisons de systèmes d’exploitation et de langages sur les appareils spécifiques.

## Étapes suivantes

Pour en savoir plus sur le développement de solutions à l’aide des appareils certifiés pour IoT, cliquez [ici](http://azure.com/iotdev).

|Appareil| Système d’exploitation testé |Langage|
|:---------|:----------|:----------|
|Raspberry Pi 2| Raspbian | C, Javascript, Java |
|Raspberry Pi 2| Windows 10 IoT Standard| C, Javascript, C#|
|BeagleBone Black| Debian |C, Javascript, Java|
|BeagleBone Green|Debian |C, Javascript, Java|
|TI CC3200 | TI-RTOS 2.x|C|
|Intel Edison |Yocto |C, Javascript|
|Minnowboard Max |Windows 7,8, 10 |C#|
|Arrow DragonBoard 410c |Windows 10 IoT Standard | C#|
|Freescale FRDM K64 |mbed 2.0 | C|

<!---HONumber=Oct15_HO2-->