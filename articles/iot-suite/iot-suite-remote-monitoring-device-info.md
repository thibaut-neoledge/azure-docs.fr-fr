---
title: "Métadonnées d’informations sur l’appareil dans la solution de surveillance à distance | Microsoft Docs"
description: "Description de la solution préconfigurée de surveillance à distance Azure IoT et de son architecture"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 25122729691867533a0489ff09770e096541d6fe
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance

La solution préconfigurée de surveillance à distance Azure IoT Suite montre une approche de gestion des métadonnées d’appareil. Cet article décrit l’approche de cette solution pour vous permettre de comprendre :

* Quelles métadonnées d’appareil sont stockées par la solution.
* Comment la solution gère les métadonnées d’appareil.

## <a name="context"></a>Context

La solution préconfigurée de surveillance à distance utilise [Azure IoT Hub][lnk-iot-hub] pour permettre à vos appareils d’envoyer des données vers le cloud. La solution stocke des informations sur les appareils dans trois emplacements différents :

| Emplacement | Informations stockées | Implémentation |
| -------- | ------------------ | -------------- |
| Registre des identités | ID de l’appareil, clés d’authentification, état Activé | Intégré à IoT Hub |
| Représentations d’appareil physique | Métadonnées : propriétés signalées, propriétés souhaitées, balises | Intégré à IoT Hub |
| Cosmos DB | Historique des commandes et méthodes | Personnalisé pour la solution |

IoT Hub inclut un [registre des identités des appareils][lnk-identity-registry] pour gérer l’accès à un IoT Hub et utilise des [jumeaux d’appareil][lnk-device-twin] pour gérer les métadonnées de l’appareil. Il inclut également un *registre des appareils* spécifique à la solution de surveillance à distance qui stocke l’historique des commandes et méthodes. La solution de surveillance à distance utilise une base de données [Cosmos DB][lnk-docdb] afin d’implémenter un magasin personnalisé pour l’historique des commandes et méthodes.

> [!NOTE]
> La solution préconfigurée de surveillance à distance synchronise le registre des identités des appareils avec les informations de la base de données Cosmos DB. Les deux utilisent le même id d’appareil pour identifier chaque appareil connecté à votre IoT hub de manière unique.

## <a name="device-metadata"></a>Métadonnées de l’appareil

IoT Hub gère un [jumeau d’appareil][lnk-device-twin] pour chaque appareil simulé et physique connecté à une solution de surveillance à distance. La solution utilise des jumeaux d’appareil pour gérer les métadonnées associées aux appareils. Un jumeau d’appareil est un document JSON géré par IoT Hub et la solution utilise l’API IoT Hub pour interagir avec les jumeaux d’appareil.

Un jumeau d’appareil stocke trois types de métadonnées :

- Les *propriétés signalées* sont envoyées à un IoT Hub par un appareil. Dans la solution de surveillance à distance, les appareils simulés envoient les propriétés signalées au démarrage et en réponse aux commandes et méthodes **Modifier l’état de l’appareil**. Vous pouvez afficher les propriétés signalées dans la **liste des appareils** et les **informations sur l’appareil** dans le portail de solution. Les propriétés signalées sont en lecture seule.
- Les *propriétés souhaitées* sont récupérées à partir d’IoT Hub IoT par les appareils. C’est l’appareil qui doit effectuer les modifications de configuration nécessaires sur l’appareil. C’est également l’appareil qui doit signaler la modification au hub sous la forme d’une propriété signalée. Vous pouvez définir une valeur de propriété souhaitée via le portail de solution.
- Les *balises* existent seulement dans le jumeau d’appareil et ne sont jamais synchronisés avec un appareil. Vous pouvez définir des valeurs de balise dans le portail de solution et les utiliser lorsque vous filtrez la liste des appareils. La solution utilise également une balise pour identifier l’icône à afficher pour un appareil dans le portail de solution.

Les propriétés signalées à partir des appareils simulés sont par exemple le fabricant, le numéro de modèle, la latitude et la longitude. Les appareils simulés retournent également la liste des méthodes prises en charge sous la forme d’une propriété signalée.

> [!NOTE]
> Le code d’appareil simulé utilise uniquement les propriétés souhaitées **Desired.Config.TemperatureMeanValue** et **Desired.Config.TelemetryInterval** pour mettre à jour les propriétés signalées renvoyées à IoT Hub. Toutes les autres demandes de modification de propriétés souhaitées sont ignorées.

Un document JSON des métadonnées relatives aux informations d’appareil stocké dans la base de données Cosmos DB du registre des appareils présente la structure suivante :

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Les informations d’appareil peuvent également inclure des métadonnées permettant de décrire la télémétrie que l’appareil envoie au IoT Hub. La solution de surveillance à distance utilise ces métadonnées de télémétrie pour personnaliser la façon dont le tableau de bord affiche la [télémétrie dynamique][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Cycle de vie

Lorsque vous créez un appareil dans le portail de solution, la solution crée une entrée dans la base de données Cosmos DB pour stocker l’historique des commandes et méthodes. À ce stade, la solution crée également une entrée pour l’appareil dans le registre des identités des appareils, qui génère les clés utilisées par l’appareil pour s’authentifier avec IoT Hub. Elle crée également un jumeau d’appareil.

Lorsqu’un appareil se connecte pour la première fois à la solution, il envoie les propriétés signalées et un message d’informations sur l’appareil. Les valeurs des propriétés signalées sont automatiquement enregistrées dans le jumeau d’appareil. Les propriétés signalées incluent le fabricant de l’appareil, le numéro du modèle, le numéro de série et une liste des méthodes prises en charge. Un message d’informations sur l’appareil inclut la liste des commandes prises en charge par l’appareil comprenant des informations sur les paramètres de commande. Lorsque la solution reçoit ce message, elle met à jour les informations de l’appareil dans la base de données Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Afficher et modifier les informations d’appareil dans le portail de la solution

La liste des appareils du portail de solution affiche par défaut les propriétés d’appareil suivantes sous forme de colonnes : **Status**, **DeviceId**, **Manufacturer**, **Model Number**, **Serial Number**, **Firmware**, **Platform**, **Processor** et **Installed RAM**. Vous pouvez personnaliser les colonnes en cliquant sur **l’éditeur de colonne**. Les propriétés d’appareil **Latitude** et **Longitude** indiquent l’emplacement dans la Carte Bing sur le tableau de bord.

![Éditeur de colonne dans la liste des appareils][img-device-list]

Dans le volet **Informations sur l’appareil** du portail de solution, vous pouvez modifier les balises et les propriétés souhaitées (les propriétés signalées sont en lecture seule).

![Volet d’informations sur l’appareil][img-device-edit]

Vous pouvez utiliser le portail de la solution pour supprimer un appareil de votre solution. Lorsque vous supprimez un appareil, la solution supprime l’entrée de l’appareil du registre d’identité, puis supprime le jumeau d’appareil. La solution supprime également les informations relatives à l’appareil de la base de données Cosmos DB. Avant de pouvoir supprimer un appareil, vous devez le désactiver.

![Supprimer l’appareil][img-device-remove]

## <a name="device-information-message-processing"></a>Traitement des messages d’information d’appareil

Les messages d’information d’appareil envoyés par un appareil sont différents des messages de télémétrie. Les messages d’information sur l’appareil incluent les commandes auxquelles répond un appareil et l’historique des commandes. IoT Hub lui-même n’a aucune connaissance des métadonnées contenues dans un message d’information d’appareil et traite le message de la même manière qu’il traite tout message appareil-à-cloud. Dans la solution de surveillance à distance, une tâche [Azure Stream Analytics][lnk-stream-analytics] (ASA) lit les messages issus de IoT Hub. Le travail **DeviceInfo** Stream Analytics filtre les messages contenant **« ObjectType » : « DeviceInfo »** et les transmet à l’instance hôte **EventProcessorHost** qui s’exécute dans une tâche web. La logique de l’instance **EventProcessorHost** utilise l’ID d’appareil pour rechercher l’enregistrement Cosmos DB de l’appareil spécifique et le mettre à jour.

> [!NOTE]
> Un message d’information d’appareil est un message appareil-à-cloud standard. La solution fait la distinction entre les messages d’information d’appareil et les messages de télémétrie en utilisant des requêtes ASA.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment personnaliser les solutions préconfigurées, vous pouvez explorer certaines des autres fonctions et fonctionnalités des solutions préconfigurées de la suite IoT :

* [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive-overview]
* [Forum Aux Questions (FAQ) relatives à IoT Suite][lnk-faq]
* [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

