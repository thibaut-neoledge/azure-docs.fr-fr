<properties
 pageTitle="Surveillance des opérations IoT Hub"
 description="Vue d’ensemble de la surveillance des opérations Azure IoT Hub, qui permet aux utilisateurs de surveiller l’état des opérations sur leur hub IoT en temps réel"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Introduction à la surveillance des opérations

La surveillance des opérations IoT Hub permet aux utilisateurs de surveiller l’état des opérations sur leur hub IoT en temps réel. IoT Hub effectue le suivi des événements entre les différentes catégories d’opérations, et les utilisateurs peuvent opter pour l’envoi des événements d’une ou plusieurs catégories à un point de terminaison de leur hub IoT en vue de leur traitement. Les utilisateurs peuvent surveiller les données des erreurs ou configurer un traitement plus complexe basé sur des modèles de données.

IoT Hub surveille quatre catégories d’événements :

- Opérations d’identité des appareils
- Télémétrie d’appareil
- Commandes cloud-à-appareil
- Connexions

## Comment activer la surveillance des opérations

1. Créez un hub IoT. Pour savoir comment créer un hub IoT, consultez le guide [Prise en main][lnk-get-started].

2. Ouvrez le panneau de votre hub IoT. Cliquez sur **Tous les paramètres**, puis cliquez sur **Surveillance des opérations**.

    ![][1]

3. Sélectionnez les catégories de surveillance qui vous intéressent, puis cliquez sur **Enregistrer**. Les événements sont disponibles en lecture depuis le point de terminaison compatible avec le hub d’événements répertorié dans **Paramètres de surveillance**.

    ![][2]

## Catégories d’événements et utilisation respective

Chaque catégorie de surveillance d’opérations assure le suivi d’un type spécifique d’interaction avec IoT Hub et a un schéma qui définit la façon dont sont structurés les événements qu’elle comporte.

### Opérations d’identité des appareils

La catégorie d’opérations d’identité des appareils effectue le suivi des erreurs qui se produisent quand l’utilisateur tente de créer, mettre à jour ou supprimer une entrée dans le registre d’identité de son hub IoT. Le suivi de cette catégorie est utile pour les scénarios d’approvisionnement.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### Télémétrie d’appareil

La catégorie de télémétrie d’appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de télémétrie. Ce sont notamment les erreurs concernant l’envoi d’événements de télémétrie (par exemple, une limitation) et la réception des événements de télémétrie (par exemple, un lecteur non autorisé). Notez que cette catégorie ne peut pas intercepter les erreurs provoquées par le code en cours d’exécution sur l’appareil lui-même.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
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

### Commandes cloud-à-appareil

La catégorie de commandes cloud-à-appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de commandes de l’appareil. Ce sont notamment les erreurs concernant l’envoi de commandes (telles qu’un expéditeur non autorisé), la réception des commandes (telles que le dépassement du nombre de remises) et la réception des commentaires de commande (telles que des commentaires arrivés à expiration). Cette catégorie n’intercepte pas d’erreurs dans le cas d’un appareil qui gère mal une commande correctement remise.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### Connexions

La catégorie de connexions effectue le suivi des événements provoqués par la connexion des appareils à un hub IoT ou leur déconnexion de celui-ci. Le suivi de cette catégorie est utile pour identifier les tentatives de connexion non autorisées et pour repérer les moments auxquels une connexion est perdue pour les appareils qui se trouvent dans des zones bénéficiant d’une connectivité médiocre.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## Étapes suivantes

La surveillance des opérations n’étant plus un secret pour vous, vous pouvez suivre les liens ci-après :

- [Métriques de diagnostic IoT Hub][lnk-diagnostic-metrics]
- [Mise à l’échelle IoT Hub][lnk-scaling]
- [Haute disponibilité et récupération d’urgence IoT Hub :][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->