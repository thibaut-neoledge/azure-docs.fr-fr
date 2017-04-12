---
title: "Comprendre les kits de développement logiciel (SDK) IoT Azure | Microsoft Docs"
description: "Guide du développeur - informations et liens vers divers kits de développement logiciels (SDK) de services et appareils Azure IoT que vous pouvez utiliser pour créer des applications d’appareil et des applications principales."
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
ms.date: 03/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: ff08ea2b6231b2344244b14e44bcfd9acd065508
ms.lasthandoff: 03/27/2017


---
# <a name="understand-and-use-azure-iot-sdks"></a>Comprendre et utiliser les Kits de développement logiciel (SDK) Azure IoT

Il existe trois catégories de Kit de développement logiciel (SDK) à utiliser avec IoT Hub :

* Les **Kits de développement logiciel (SDK) d’appareil** vous permettent de générer des applications qui s’exécuteront sur vos appareils IoT. Ces applications envoient des données de télémétrie à votre hub IoT et reçoivent éventuellement des messages provenant de votre hub IoT.

* Les **Kits de développement logiciel (SDK) de service** vous permettent de gérer votre hub IoT et éventuellement d’envoyer des messages à vos appareils IoT.

* Les **Kits de développement logiciel (SDK) de passerelle** vous permettent de créer des passerelles pour activer les appareils qui n’utilisent pas l’un des protocoles pris en charge, ou lorsque vous devez traiter des messages en périphérie.

Les Kits de développement logiciel (SDK) prennent en charge plusieurs langages de programmation.

## <a name="azure-iot-device-sdks"></a>Kits de développement logiciel (SDK) d’appareils Azure IoT

Les Kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’appareils et d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

Les Kits Azure IoT device SDK suivants peuvent être téléchargés à partir de GitHub :

* [Azure IoT device SDK pour C][lnk-c-device-sdk] : écrit en C ANSI (C99) pour la portabilité et la compatibilité de nombreuses plateformes.
* [Azure IoT device SDK pour .NET][lnk-dotnet-device-sdk]
* [Azure IoT device SDK pour Java][lnk-java-device-sdk]
* [Azure IoT device SDK pour Node.js][lnk-node-device-sdk]
* [Azure IoT device SDK pour Python][lnk-python-device-sdk]

> [!NOTE]
> Consultez les fichiers readme dans les référentiels GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel

Pour plus d’informations sur la compatibilité du Kit de développement logiciel (SDK) avec des appareils physiques spécifiques, consultez le [Catalogue d’appareils certifiés Azure pour l’IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Kits de développement logiciel (SDK) de services Azure IoT

Les Kits de développement logiciel (SDK) du service Azure IoT contiennent du code pour faciliter la création d’applications qui interagissent directement avec IoT Hub pour gérer les appareils et la sécurité.

Les Kits Azure IoT service SDK suivants peuvent être téléchargés à partir de GitHub :

* [Azure IoT service SDK pour .NET][lnk-dotnet-service-sdk]
* [Azure IoT service SDK pour Node.js][lnk-node-service-sdk]
* [Azure IoT service SDK pour Java][lnk-java-service-sdk]
* [Azure IoT service SDK pour Java][lnk-python-service-sdk]


> [!NOTE]
> Consultez les fichiers readme dans les référentiels GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.

## <a name="azure-iot-gateway-sdks"></a>Kits de développement logiciel (SDK) de la passerelle Azure IoT

Ce Kit de développement logiciel (SDK) de la passerelle Azure IoT contient l’infrastructure et les modules nécessaires pour créer des solutions de passerelle IoT. Vous pouvez étendre le Kit de développement logiciel (SDK) pour créer des passerelles adaptées à n’importe quel scénario de bout en bout.

Vous pouvez télécharger le [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk] à partir de GitHub.

## <a name="online-api-reference-documentation"></a>Documentation de référence d’API en ligne

La liste suivant contient des liens de documentation de référence d’API en ligne pour les bibliothèques d’appareils, de services et de passerelles Azure IoT :

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

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/azure-iot-device/1.1.9/index.html
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/azure-iothub/1.1.9/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

