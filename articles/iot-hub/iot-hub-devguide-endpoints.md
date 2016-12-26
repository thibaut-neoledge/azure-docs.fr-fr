---
title: "Guide du développeur - Points de terminaison IoT Hub | Microsoft Docs"
description: "Guide du développeur Azure IoT Hub - Informations de référence sur les points de terminaison IoT Hub"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: f2da46ba3fdf9386ad78ade4d2364a4a8990cd6d


---
# <a name="reference---iot-hub-endpoints"></a>Référence - Points de terminaison IoT Hub
## <a name="list-of-iot-hub-endpoints"></a>Liste de points de terminaison IoT Hub
Azure IoT Hub est un service mutualisé qui propose ses fonctionnalités à différents acteurs. Le schéma qui suit illustre les différents points de terminaison proposés par IoT Hub.

![Points de terminaison IoT Hub][img-endpoints]

Vous trouverez ci-dessous une description des points de terminaison :

* **Fournisseur de ressources**. Le fournisseur de ressources IoT Hub expose une interface [Azure Resource Manager][lnk-arm] qui permet aux titulaires d’abonnements Azure de créer des IoT Hubs, de les supprimer et de mettre à jour leurs propriétés. Les propriétés des IoT Hubs régissent les [stratégies de sécurité au niveau du hub][lnk-accesscontrol], par opposition au contrôle d’accès au niveau de l’appareil, et les options fonctionnelles pour les messages cloud-à-appareil et appareil-à-cloud. Le fournisseur de ressources IoT Hub vous permet également [d’exporter les identités des appareils][lnk-importexport].
* **Gestion d’identité de l’appareil**. Chaque IoT Hub expose un ensemble de points de terminaison HTTP REST afin de gérer les identités des appareils (par exemple, pour les opérations de création, de récupération, de mise à jour et de suppression). Les [identités des appareils][lnk-device-identities] sont utilisées pour l’authentification des appareils et le contrôle d’accès.
* **Gestion des représentations d’appareils**. Chaque IoT Hub expose un ensemble de points de terminaison REST HTTP orientés service pour interroger et mettre à jour les [représentations d’appareil][lnk-twins] (mise à jour des balises et des propriétés).
* **Gestion des travaux**. Chaque IoT Hub expose un ensemble de points de terminaison REST HTTP orientés service pour interroger et gérer les [travaux][lnk-jobs].
* **Points de terminaison des appareils**. Pour chaque appareil approvisionné dans le registre des identités, IoT Hub expose un ensemble de points de terminaison qu’un appareil peut utiliser pour envoyer et recevoir des messages :
  
  * *Envoyer des messages appareil-à-cloud*. Utilisez ce point de terminaison pour [envoyer des messages appareil-à-cloud][lnk-d2c].
  * *Recevoir des messages cloud-à-appareil*. Un appareil utilise ce point de terminaison pour recevoir les [messages cloud-à-appareil][lnk-c2d] qui lui sont adressés.
  * *Initier des téléchargements de fichiers*. Un appareil utilise ce point de terminaison pour recevoir un URI SAP du Stockage Azure à partir d’IoT Hub pour [charger un fichier][lnk-upload].
  * *Récupérer et mettre à jour les propriétés d’une représentation d’appareil*. Un appareil utilise ces points de terminaison pour accéder aux propriétés de sa [représentation][lnk-twins].
  * *Recevoir des requêtes de méthodes directes*. Un appareil utilise ces points de terminaison pour écouter les requêtes de [méthodes directes][lnk-methods].
    
    Ces points de terminaison sont exposés à l’aide des protocoles [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 et [AMQP 1.0][lnk-amqp]. Notez que le protocole AMQP est également disponible sur [WebSockets][lnk-websockets], sur le port 443.
    
    Les représentations d’appareil et points de terminaison des méthodes sont disponibles uniquement à l’aide de [MQTT v3.1.1][lnk-mqtt].
* **Points de terminaison de service**. Chaque IoT Hub expose un ensemble de points de terminaison que votre système principal d’application peut utiliser pour communiquer avec vos appareils. Ces points de terminaison sont actuellement uniquement exposés à l’aide du protocole [AMQP][lnk-amqp], sauf pour le point de terminaison d’appel de méthode, qui est exposé via HTTP 1.1.
  
  * *Recevoir des messages Appareil vers cloud*. Ce point de terminaison est compatible avec [Azure Event Hubs][lnk-event-hubs]. Il peut être utilisé par un service principal pour lire tous les [messages appareil-à-cloud][lnk-d2c] envoyés par vos appareils.
  * *Envoyer des messages Cloud vers appareil et recevoir des accusés de remise*. Ces points de terminaison autorisent votre serveur principal d’application à envoyer des [messages cloud-à-appareil][lnk-c2d] et à recevoir les accusés de réception ou d’expiration correspondants.
  * *Recevoir les notifications de fichier*. Ce point de terminaison de messagerie vous permet de recevoir des notifications lorsqu’un fichier est correctement téléchargé sur votre appareil. 
  * *Invocation de méthode directe*. Ce point de terminaison permet à un service principal d’appeler une [méthode directe][lnk-methods] sur un appareil.

L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks] décrit les différentes méthodes permettant d’accéder à ces points de terminaison.

Enfin, il est important de noter que tous les points de terminaison IoT Hub utilisent le protocole [TLS][lnk-tls] et qu’aucun point de terminaison n’est jamais exposé sur des canaux non chiffrés/non sécurisés.

## <a name="field-gateways"></a>Passerelles de champ
Dans une solution IoT, une *passerelle de champ* se situe entre vos appareils et vos points de terminaison IoT Hub. Elle est généralement située près de vos appareils. Vos appareils communiquent directement avec la passerelle de champ à l’aide d’un protocole pris en charge. La passerelle de champ se connecte à un point de terminaison IoT Hub à l’aide d’un protocole pris en charge par ce dernier. Une passerelle de champ peut être un matériel très spécialisé ou un ordinateur à faible consommation d'énergie exécutant un logiciel qui accomplit de bout en bout le scénario pour lequel la passerelle est prévue.

Vous pouvez utiliser le [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk] pour implémenter une passerelle de champ. Ces Kits de développement logiciel (SDK) offrent des fonctionnalités spécifiques comme la possibilité de multiplexer la communication à partir de plusieurs appareils sur la même connexion à IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* [Langage de requête d’IoT Hub pour les représentations d’appareil et les travaux][lnk-devguide-query]
* [Quotas et limitation][lnk-devguide-quotas]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


