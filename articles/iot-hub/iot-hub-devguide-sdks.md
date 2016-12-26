---
title: "Guide du développeur - SDK Azure IoT | Microsoft Docs"
description: "Guide du développeur Azure IoT Hub - Informations et liens sur les différents Kits Azure IoT device et service SDK."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58dc546ee2db9a0925ecf0fc074795b531323e03


---
# <a name="azure-iot-sdks"></a>Kits de développement logiciel (SDK) Azure IoT
## <a name="azure-iot-device-sdk"></a>Azure IoT device SDK
Les Kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’appareils et d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

Les Kits Azure IoT device SDK suivants peuvent être téléchargés à partir de GitHub :

* [Azure IoT device SDK pour C][lnk-c-device-sdk] : écrit en C ANSI (C99) pour la portabilité et la compatibilité de nombreuses plateformes.
* [Azure IoT device SDK pour .NET][lnk-dotnet-device-sdk]
* [Azure IoT device SDK pour Java][lnk-java-device-sdk]
* [Azure IoT device SDK pour Node.js][lnk-node-device-sdk]
* [Microsoft Azure IoT device SDK pour Python 2.7][lnk-python-device-sdk]

> [!NOTE]
> Consultez les fichiers readme dans les référentiels GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel
Pour plus d’informations sur la compatibilité du Kit de développement logiciel (SDK) avec des appareils physiques spécifiques, consultez le [Catalogue d’appareils certifiés Azure pour l’IoT][lnk-certified].

## <a name="azure-iot-service-sdk"></a>Azure IoT service SDK
Azure IoT service SDK contient du code qui facilite la création d’applications interagissant directement avec IoT Hub pour gérer les appareils et la sécurité.

Les Kits Azure IoT service SDK suivants peuvent être téléchargés à partir de GitHub :

* [Azure IoT service SDK pour .NET][lnk-dotnet-service-sdk]
* [Azure IoT service SDK pour Node.js][lnk-node-service-sdk]
* [Azure IoT service SDK pour Java][lnk-java-service-sdk]

> [!NOTE]
> Consultez les fichiers readme dans les référentiels GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

## <a name="azure-iot-gateway-sdk"></a>Kit de développement logiciel (SDK) de la passerelle Azure IoT
Ce Kit de développement logiciel (SDK) de la passerelle Azure IoT contient l’infrastructure et les modules nécessaires pour créer des solutions de passerelle IoT. Vous pouvez étendre le Kit de développement logiciel (SDK) pour créer des passerelles adaptées à n’importe quel scénario de bout en bout.

Vous pouvez télécharger le [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk] à partir de GitHub.

## <a name="online-api-reference-documentation"></a>Documentation de référence d’API en ligne
Voici une liste de liens de documentation de référence d’API en ligne pour les bibliothèques d’appareils, de services et de passerelles Azure IoT :

* [Internet des objets (IoT) .NET][lnk-dotnet-ref]
* [IoT Hub REST][lnk-rest-ref]
* [Azure IoT device SDK pour C][lnk-c-ref]
* [Azure IoT device SDK pour Java][lnk-java-ref]
* [Azure IoT service SDK pour Java][lnk-java-service-ref]
* [Azure IoT device SDK pour Node.js][lnk-node-ref]
* [Azure IoT service SDK pour Node.js][lnk-node-service-ref]
* [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-ref]

## <a name="next-steps"></a>Étapes suivantes
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub][lnk-devguide-endpoints]
* [Langage de requête d’IoT Hub pour les représentations d’appareil et les travaux][lnk-devguide-query]
* [Quotas et limitation][lnk-devguide-quotas]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


