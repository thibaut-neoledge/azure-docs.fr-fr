---
title: Liste des Kits SDK Azure IoT Hub | Microsoft Docs
description: Informations et liens sur les différents Kits SDK pour les appareils et services Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: dobett

---
# Kits SDK IoT Hub
Cet article fournit des informations sur les différents [Kits SDK Microsoft Azure IoT][Kits SDK Microsoft Azure IoT] ainsi que des liens vers des ressources supplémentaires.

## Kits IoT Hub device SDK
Les Kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’appareils et d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

Les Kits IoT device SDK suivants peuvent être téléchargés à partir de GitHub :

* [Azure IoT device SDK pour C][Azure IoT device SDK pour C] : écrit en C ANSI (C99) pour la portabilité et la compatibilité de nombreuses plateformes.
* [Azure IoT device SDK pour .NET][Azure IoT device SDK pour .NET]
* [ Azure IoT device SDK pour Java][ Azure IoT device SDK pour Java]
* [Azure IoT device SDK pour Node.js][Azure IoT device SDK pour Node.js]
* [Microsoft Azure IoT device SDK pour Python 2.7][Microsoft Azure IoT device SDK pour Python 2.7]

### Compatibilité des plateformes de système d’exploitation et du matériel
Pour plus d’informations sur la compatibilité avec des appareils matériels spécifiques, consultez les articles suivants :

* [Compatibilité des plateformes de système d’exploitation et du matériel avec les Kits SDK d’appareil][OS Platforms and hardware compatibility]
* [Programme Microsoft Azure Certified pour IoT][Programme Microsoft Azure Certified pour IoT].

## Kits IoT Hub service SDK
Les Kits Microsoft Azure IoT service SDK contiennent du code qui facilite la création d’applications qui interagissent directement avec IoT Hub pour gérer les appareils et la sécurité.

Les Kits IoT service SDK suivants peuvent être téléchargés à partir de GitHub :

* [Azure IoT service SDK pour Node.js][Azure IoT service SDK pour Node.js]
* [Azure IoT service SDK pour Java][Azure IoT service SDK pour Java]

## Kit de développement logiciel (SDK) de la passerelle Azure IoT
Ce Kit de développement logiciel (SDK) de la passerelle Azure IoT contient l’infrastructure et les modules nécessaires pour créer des solutions de passerelle IoT. Vous pouvez étendre le Kit de développement logiciel (SDK) pour créer des passerelles adaptées à n’importe quel scénario de bout en bout.

Vous pouvez télécharger le [Kit de développement logiciel (SDK) de la passerelle Azure IoT][Kit de développement logiciel (SDK) de la passerelle Azure IoT] à partir de GitHub.

## Documentation de référence d’API en ligne
Voici une liste de liens de documentation de référence d’API en ligne pour les bibliothèques d’appareils, de services et de passerelles Azure IoT :

* [Internet des objets (IoT) .NET][Internet des objets (IoT) .NET]
* [IoT Hub REST][IoT Hub REST]
* [Microsoft Azure IoT device SDK pour C][Microsoft Azure IoT device SDK pour C]
* [Microsoft Azure IoT device SDK pour Java][Microsoft Azure IoT device SDK pour Java]
* [Microsoft Azure IoT service SDK pour Java][Microsoft Azure IoT service SDK pour Java]
* [Microsoft Azure IoT device SDK pour Node.js][Microsoft Azure IoT device SDK pour Node.js]
* [Microsoft Azure IoT service SDK pour Node.js][Microsoft Azure IoT service SDK pour Node.js]
* [Kit de développement logiciel (SDK) de la passerelle Microsoft Azure IoT][Kit de développement logiciel (SDK) de la passerelle Microsoft Azure IoT]

## Étapes suivantes
Pour explorer davantage les capacités de IoT Hub, consultez :

* [Conception de votre solution][lnk-design]
* [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]
* [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

[Kits SDK Microsoft Azure IoT]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[Azure IoT device SDK pour C]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[Azure IoT device SDK pour .NET]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[ Azure IoT device SDK pour Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[Azure IoT service SDK pour Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[Azure IoT device SDK pour Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[Azure IoT service SDK pour Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[Microsoft Azure IoT device SDK pour Python 2.7]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[OS Platforms and hardware compatibility]: iot-hub-tested-configurations.md
[Programme Microsoft Azure Certified pour IoT]: iot-hub-tested-configurations.md#microsoft-azure-certified-for-iot
[Kit de développement logiciel (SDK) de la passerelle Azure IoT]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[Internet des objets (IoT) .NET]: https://msdn.microsoft.com/library/mt488521.aspx
[Microsoft Azure IoT device SDK pour C]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[Microsoft Azure IoT device SDK pour Java]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[Microsoft Azure IoT device SDK pour Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.14/index.html
[IoT Hub REST]: https://msdn.microsoft.com/library/mt548492.aspx
[Microsoft Azure IoT service SDK pour Java]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[Microsoft Azure IoT service SDK pour Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.16/index.html
[Kit de développement logiciel (SDK) de la passerelle Microsoft Azure IoT]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->