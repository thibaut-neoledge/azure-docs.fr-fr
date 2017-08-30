---
title: "Présentation de la solution préconfigurée de surveillance à distance | Microsoft Docs"
description: "Description de la solution préconfigurée de surveillance à distance Azure IoT et de son architecture"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: c6d76cc741a6d932a506017781e45bc9b8f8c640
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Présentation de la solution préconfigurée de surveillance à distance

La [solution préconfigurée][lnk-preconfigured-solutions] de surveillance à distance IoT Suite est une implémentation d’une solution de surveillance de bout en bout destinée à plusieurs ordinateurs distants. Combinant les principaux services Azure pour fournir une implémentation générique du scénario d’entreprise. Vous pouvez utiliser la solution comme point de départ pour votre propre implémentation et la [personnaliser][lnk-customize] pour répondre à vos propres exigences professionnelles.

Cet article vous familiarise avec les éléments clés de la solution de surveillance à distance pour vous permettre de comprendre son fonctionnement. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels. 
* Concevoir votre propre solution IoT utilisant des services Azure.

## <a name="logical-architecture"></a>Architecture logique

Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![Architecture logique](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Simulations d’appareils

Dans la solution préconfigurée, l’appareil simulé représente un système de refroidissement (un climatiseur dans un bâtiment ou une unité de traitement d’air dans une usine, par exemple). Lorsque vous déployez la solution préconfigurée, vous configurez automatiquement quatre appareils simulés qui s’exécutent dans une [tâche web Azure][lnk-webjobs]. Les appareils simulés vous permettent d’explorer plus facilement le comportement de la solution sans avoir à déployer des appareils physiques. Pour déployer un appareil physique réel, consultez le didacticiel [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Messages Appareil vers cloud

Chaque appareil simulé peut envoyer les types de messages suivants à IoT Hub :

| Message | Description |
| --- | --- |
| Démarrage |Lorsque l’appareil démarre, il envoie un message **device-info** contenant des informations sur lui-même au serveur principal. Ce message inclut l’ID de l’appareil et une liste des commandes et méthodes qu’il prend en charge. |
| Présence |Un appareil envoie régulièrement un message de **présence** afin de signaler s’il détecte la présence d’un capteur. |
| Télémétrie |Un appareil envoie régulièrement un message de **télémétrie** qui indique les valeurs simulées de température et d’humidité, collectées par les capteurs simulés de l’appareil. |

> [!NOTE]
> La solution stocke la liste des commandes prises en charge par l’appareil dans une base de données Cosmos DB et non dans la représentation de l’appareil.

### <a name="properties-and-device-twins"></a>Propriétés et représentations d’appareil

Les appareils simulés envoient les propriétés d’appareil suivantes sur la [représentation][lnk-device-twins] dans IoT Hub en tant que *propriétés rapportées*. L’appareil envoie les propriétés signalées au démarrage et en réponse à une commande ou méthode **Changer l’état de l’appareil**.

| Propriété | Objectif |
| --- | --- |
| Config.TelemetryInterval | Fréquence (en secondes) d’envoi de télémétrie par l’appareil |
| Config.TemperatureMeanValue | Spécifie la valeur moyenne de la télémétrie de température simulée |
| Device.DeviceID |Identifiant fourni ou attribué au moment lorsqu’un appareil est créé dans la solution |
| Device.DeviceState | État signalé par l’appareil |
| Device.CreatedTime |Heure à laquelle l’appareil a été créé dans la solution |
| Device.StartupTime |Heure de démarrage de l’appareil |
| Device.LastDesiredPropertyChange |Le numéro de version du dernier changement de propriété souhaité |
| Device.Location.Latitude |Emplacement en latitude de l’appareil |
| Device.Location.Longitude |Emplacement en longitude de l’appareil |
| System.Manufacturer |Fabricant de l'appareil |
| System.ModelNumber |Numéro de modèle de l’appareil |
| System.SerialNumber |Numéro de série de l’appareil |
| System.FirmwareVersion |Version actuelle du microprogramme installé sur l’appareil |
| System.Platform |Architecture de la plate-forme de l’appareil |
| System.Processor |Processeur sur lequel est exécuté l’appareil |
| System.InstalledRAM |Quantité de RAM installée sur l’appareil |

Le simulateur amorce ces propriétés dans les appareils simulés avec des exemples de valeurs. À chaque fois que le simulateur initialise un appareil simulé, l’appareil publie les métadonnées prédéfinies dans IoT Hub en tant que propriétés rapportées. Les propriétés signalées peuvent uniquement être mises à jour par l’appareil. Pour modifier une propriété signalée, définissez une propriété souhaitée dans portail le de la solution. Il tient de la responsabilité de l’appareil de :

1. Récupérez régulièrement les propriétés souhaitées à partir de IoT Hub.
2. Mettez à jour sa configuration avec la valeur de propriété de votre choix.
3. Renvoyez la nouvelle valeur au hub comme propriété signalée.

À partir du tableau de bord de la solution, vous pouvez utiliser les *propriétés souhaitées* pour définir des propriétés sur un appareil à l’aide de la [représentation d’appareil][lnk-device-twins]. En règle générale, un appareil lit une valeur de propriété souhaitée à partir du hub pour mettre à jour son état interne et signaler le changement en tant que propriété signalée.

> [!NOTE]
> Le code d’appareil simulé utilise uniquement les propriétés souhaitées **Desired.Config.TemperatureMeanValue** et **Desired.Config.TelemetryInterval** pour mettre à jour les propriétés signalées renvoyées à IoT Hub. Toutes les autres demandes de modification de propriétés souhaitées sont ignorées dans l’appareil simulé.

### <a name="methods"></a>Méthodes

Les appareils simulés peuvent prendre en charge les méthodes suivantes ([méthodes directes][lnk-direct-methods]) appelées depuis le portail de la solution via IoT Hub :

| Méthode | Description |
| --- | --- |
| InitiateFirmwareUpdate |Demande à l’appareil d’effectuer une mise à jour du microprogramme |
| Reboot |Demande à l’appareil de redémarrer |
| FactoryReset |Demande à l’appareil d’effectuer une réinitialisation aux paramètres d’usine |

Certaines méthodes utilisent les propriétés signalées pour indiquer la progression. Par exemple, la méthode **InitiateFirmwareUpdate** démarre une mise à jour asynchrone que l’appareil. La méthode retourne immédiatement sur l’appareil, alors que la tâche asynchrone continue à renvoyer des mises à jour d’état au tableau de bord de la solution à l’aide des propriétés signalées.

### <a name="commands"></a>Commandes

Les appareils simulés peuvent prendre en charge les commandes suivantes (messages cloud-à-cloud), envoyées à partir du portail de bord de la solution via IoT Hub :

| Commande | Description |
| --- | --- |
| PingDevice |Envoie une commande *ping* à l’appareil pour vérifier s’il est actif |
| StartTelemetry |Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry |Commande à l’appareil de cesser l’envoi de données de télémétrie |
| ChangeSetPointTemp |Modifie la valeur nominale autour de laquelle sont générées les données aléatoires |
| DiagnosticTelemetry |Déclenche le simulateur d’appareil pour envoyer une valeur de télémétrie supplémentaire (externalTemp) |
| ChangeDeviceState |Modifie une propriété de l’état étendu de l’appareil et envoie le message d’informations sur l’appareil à partir de l’appareil |

> [!NOTE]
> Pour voir une comparaison de ces commandes (cloud-à-appareil) et des méthodes (méthodes directes), consultez [Conseils pour les communications cloud-à-appareil][lnk-c2d-guidance].

## <a name="iot-hub"></a>IoT Hub

[IoT Hub][lnk-iothub] ingère les données envoyées par les appareils au cloud et les rend disponibles pour les tâches Azure Stream Analytics (ASA). Chaque tâche ASA utilise un groupe de consommateurs IoT Hub distinct pour lire le flux de messages émanant de vos appareils.

L’instance IoT Hub de la solution effectue également ce qui suit :

- Conserve un registre des identités contenant les identifiants et clés d’authentification de tous les appareils autorisés à se connecter au portail. Vous pouvez activer et désactiver des appareils via le Registre des identités.
- Envoie des commandes à vos appareils pour le compte du portail de la solution.
- Appelle des méthodes sur vos appareils pour le compte du portail de la solution.
- Gère les représentations d’appareil pour tous les appareils inscrits. Une représentation d’appareil stocke les valeurs des propriétés signalées par un appareil. Une représentation d’appareil stocke également les propriétés souhaitées, définies dans le portail de la solution, que l’appareil récupère lors de sa connexion suivante.
- Planifie des travaux pour définir des propriétés pour plusieurs appareils ou appeler des méthodes sur plusieurs appareils.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Dans la solution de surveillance à distance, [Azure Stream Analytics][lnk-asa] (ASA) distribue les messages reçus par IoT Hub à d’autres composants du serveur principal, en vue de leur traitement ou de leur stockage. Différentes tâches ASA exécutent des fonctions spécifiques en fonction du contenu des messages.

**tâche 1 : informations de l’appareil** filtre les messages d’information de l’appareil provenant du flux de messages entrants et les envoie à un point de terminaison du hub d’événements. Un appareil envoie des messages d’information au démarrage et en réponse à une commande **SendDeviceInfo** . Cette tâche utilise la définition de requête suivante pour identifier les messages **device-info** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Cette tâche envoie ses résultats à un Event Hub pour un traitement ultérieur.

**tâche 2 : règles** compare les valeurs de télémétrie entrantes (température et humidité) aux seuils définis pour chaque appareil. Les valeurs de seuil sont définies dans l’éditeur de règles disponible dans le portail. Chaque paire appareil/valeur est stockée par horodatage dans un objet blob que Stream Analytics lit comme des **Données de référence**. La tâche compare toutes les valeurs non vides au seuil défini pour l’appareil. En cas de dépassement de la condition « > », elle génère un événement **d’alarme** indiquant que le seuil a été dépassé et spécifie l’appareil, la valeur ainsi que les valeurs d’horodatage. Cette tâche utilise la définition de requête suivante pour identifier les messages de télémétrie qui doivent déclencher une alarme :

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

La tâche envoie ses résultats à un Event Hub pour un traitement ultérieur et enregistre les détails de chaque alerte dans le stockage d’objets blob à partir duquel le portail de la solution peut lire les informations d’alerte.

**tâche 3 : télémétrie** agit sur le flux de télémétrie entrant de l’appareil, de deux manières différentes. La première consiste à transférer tous les messages de télémétrie des appareils vers le stockage persistant d’objets blob pour un stockage à long terme. La deuxième calcule les valeurs d’humidité moyenne, minimale et maximale sur une fenêtre glissante de cinq minutes, et envoie ces données au stockage d’objets blob. Le portail de la solution lit les données de télémétrie à partir du stockage d’objets blob pour remplir les graphiques. Cette tâche utilise la définition de requête suivante :

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Event Hubs

Les tâches ASA **informations sur l’appareil** et **règles** envoient leurs données à Event Hubs qui transfère les résultats au **Processeur d’événements** exécuté dans le WebJob.

## <a name="azure-storage"></a>Stockage Azure

La solution utilise le stockage d’objets blob Azure pour conserver toutes les données de télémétrie brutes et résumées provenant des appareils dans la solution. Le portail lit les données de télémétrie à partir du stockage d’objets blob pour remplir les graphiques. Pour afficher les alertes, le portail de la solution lit les données du stockage d’objets blob qui enregistre tout dépassement des valeurs de télémétrie par rapport aux valeurs de seuil configurées. La solution utilise également le stockage d’objets blob pour enregistrer les valeurs de seuil définies par un utilisateur dans le portail de la solution.

## <a name="webjobs"></a>WebJobs

Outre les simulateurs d’appareil, les tâches web de la solution hébergent des **processeurs d’événements** exécutés dans une tâche web Azure, qui gère les réponses aux commandes. Les messages de réponse aux commandes sont utilisés pour mettre à jour l’historique des commandes de l’appareil (stocké dans la base de données Cosmos DB).

## <a name="cosmos-db"></a>Cosmos DB

La solution utilise une base de données Cosmos DB pour stocker des informations sur les appareils connectés à la solution. Ces informations comprennent l’historique des commandes envoyées aux appareils à partir du portail de la solution et des méthodes appelées depuis le portail de la solution.

## <a name="solution-portal"></a>Portail de la solution

Le portail de la solution est une application web déployée dans le cadre de la solution préconfigurée. Les pages essentielles dans le portail de la solution sont le tableau de bord et la liste des appareils.

### <a name="dashboard"></a>Tableau de bord

Cette page de l’application web utilise les contrôles Javascript PowerBI (consultez le [référentiel d’éléments visuels PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) pour visualiser les données de télémétrie provenant des appareils. La solution utilise la tâche de télémétrie ASA pour écrire les données de télémétrie dans le stockage d’objets blob.

### <a name="device-list"></a>Liste des appareils

Sur cette page du portail de la solution, vous pouvez :

* Configurer un nouvel appareil. Cela vous permet de définir l’identifiant unique de l’appareil et de générer la clé d’authentification. Les informations relatives à l’appareil sont inscrites à la fois dans le registre d’identités IoT Hub et dans la base de données Cosmos DB de la solution.
* Gérer les propriétés de l’appareil. Cela comprend l’affichage des propriétés existantes et l’intégration des nouvelles propriétés.
* envoyer des commandes à un appareil ;
* afficher l’historique de commande d’un appareil.
* Désactiver et activer les appareils.

## <a name="next-steps"></a>Étapes suivantes

Les billets de blog TechNet suivants fournissent des détails supplémentaires sur la solution préconfigurée de surveillance à distance :

* [IoT Suite - Under The Hood - Remote Monitoring (en anglais)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (en anglais)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Vous pouvez poursuivre la prise en main d’IoT Suite en lisant les articles suivants :

* [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm]
* [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md

