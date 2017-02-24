---
title: "Surveillance des opérations Azure IoT Hub | Microsoft Docs"
description: "Guide pratique d’utilisation des opérations d’Azure IoT Hub pour la surveillance des opérations IoT Hub afin de surveiller l’état des opérations sur votre hub IoT en temps réel."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 8f72f2ca66a5d1394e87c7c0f8d8dff9da73732f
ms.openlocfilehash: 612ef94efb9776ae0ce768de1b59fb208824da93


---
# <a name="iot-hub-operations-monitoring"></a>Surveillance des opérations IoT Hub
La surveillance des opérations IoT Hub vous permet de surveiller l’état des opérations sur votre hub IoT en temps réel. IoT Hub effectue le suivi des événements entre différentes catégories d’opérations. Vous pouvez opter pour l’envoi des événements d’une ou plusieurs catégories à un point de terminaison de votre IoT Hub en vue de leur traitement. Vous pouvez surveiller les données des erreurs ou configurer un traitement plus complexe basé sur des modèles de données.

IoT Hub surveille six catégories d’événements :

* Opérations d’identité des appareils
* Télémétrie d’appareil
* Messages Cloud vers appareil
* Connexions
* Chargements de fichiers
* Routage de messages

## <a name="how-to-enable-operations-monitoring"></a>Comment activer la surveillance des opérations
1. Créez un hub IoT. Pour savoir comment créer un IoT Hub, consultez le guide [Prise en main][lnk-get-started].
2. Ouvrez le panneau de votre hub IoT. De là, cliquez sur **Surveillance des opérations**.
   
    ![][1]
3. Sélectionnez les catégories de surveillance qui vous intéressent, puis cliquez sur **Enregistrer**. Les événements sont disponibles en lecture depuis le point de terminaison compatible avec le hub d’événements répertorié dans **Paramètres de surveillance**. Le point de terminaison IoT Hub s’appelle `messages/operationsmonitoringevents`.
   
    ![][2]

> [!NOTE]
> Si vous sélectionnez la surveillance **détaillée** dans la catégorie **Connexions**, IoT Hub génère des messages de diagnostic supplémentaires. Pour toutes les autres catégories, le paramètre **Détaillée** modifie la quantité d’informations qu’IoT Hub inclut dans chaque message d’erreur.

## <a name="event-categories-and-how-to-use-them"></a>Catégories d’événements et utilisation respective
Chaque catégorie de surveillance d’opérations assure le suivi d’un type spécifique d’interaction avec IoT Hub et a un schéma qui définit la façon dont sont structurés les événements qu’elle comporte.

### <a name="device-identity-operations"></a>Opérations d’identité des appareils
La catégorie d’opérations d’identité des appareils effectue le suivi des erreurs qui se produisent quand vous tentez de créer, mettre à jour ou supprimer une entrée dans le registre d’identité de votre hub IoT. Le suivi de cette catégorie est utile pour les scénarios d’approvisionnement.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Télémétrie d’appareil
La catégorie de télémétrie d’appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de télémétrie. Cette catégorie inclut notamment les erreurs concernant l’envoi d’événements de télémétrie (par exemple, une limitation) et la réception des événements de télémétrie (par exemple, un lecteur non autorisé). Notez que cette catégorie ne peut pas intercepter les erreurs provoquées par le code en cours d’exécution sur l’appareil lui-même.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Commandes cloud-à-appareil
La catégorie de commandes cloud-à-appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de messages cloud-à-appareil. Cette catégorie inclut notamment les erreurs concernant l’envoi de messages cloud-à-appareil (telles qu’un expéditeur non autorisé), la réception des messages cloud-à-appareil (telles que le dépassement du nombre de remises) et la réception des commentaires de message cloud-à-appareil (telles que des commentaires arrivés à expiration). Cette catégorie n’intercepte pas d’erreurs dans le cas d’un appareil qui gère mal un message cloud-à-appareil si le message a été correctement remis.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Connexions
La catégorie de connexions effectue le suivi des erreurs provoquées par la connexion des appareils à un hub IoT ou leur déconnexion de celui-ci. Le suivi de cette catégorie est utile pour identifier les tentatives de connexion non autorisées et pour repérer les moments auxquels une connexion est perdue pour les appareils qui se trouvent dans des zones bénéficiant d’une connectivité médiocre.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Chargements de fichiers

La catégorie de chargement de fichiers effectue le suivi des erreurs qui se produisent au niveau de l’IoT hub et qui sont liées à la fonctionnalité de chargement. Cette catégorie inclut :

- Erreurs qui se produisent avec l’URI SAP, par exemple en cas d’expiration avant qu’un appareil notifie le hub d’un téléchargement terminé.
- Échecs des téléchargements signalés par l’appareil.
- Erreurs qui se produisent lorsqu’un fichier est introuvable dans le stockage lors de la création du message de notification IoT Hub.

Notez que cette catégorie ne peut pas détecter les erreurs qui surviennent directement pendant que le périphérique charge un fichier de stockage.


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

### <a name="message-routing"></a>Routage de messages
La catégorie de routage des messages assure le suivi des erreurs qui se produisent pendant l’évaluation du routage des messages et de l’intégrité du point de terminaison perçue par IoT Hub. Cette catégorie inclut les événements tels qu’une règle prenant la valeur « Non définie », lorsqu’IoT Hub marque un point de terminaison comme inactif et toute autre erreur reçue à partir d’un point de terminaison. Cette catégorie n’inclut pas les erreurs spécifiques sur les messages eux-mêmes (tels que les erreurs de limitation des appareils) qui sont signalées dans la catégorie « télémétrie des appareils ».
        
    {
        "messageSizeInBytes": 1234,
        "time": "UTC timestamp",
        "operationName": "ingress",
        "category": "routes",
        "level": "Error",
        "deviceId": "device-ID",
        "messageId": "ID of message",
        "routeName": "myroute",
        "endpointName": "myendpoint",
        "details": "ExternalEndpointDisabled"
    }

## <a name="next-steps"></a>Étapes suivantes
Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) de la passerelle IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Feb17_HO2-->


