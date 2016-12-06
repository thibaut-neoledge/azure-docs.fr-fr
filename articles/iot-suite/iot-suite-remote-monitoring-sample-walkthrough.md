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
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1a6dd35278f0a4a4f972642c40a0976986dd79ae
ms.openlocfilehash: db1cd76d4a99b2b25cc9589f131f3d8da3c2d84a


---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Présentation de la solution préconfigurée de surveillance à distance
## <a name="introduction"></a>Introduction
La [solution préconfigurée][lnk-preconfigured-solutions] de surveillance à distance IoT Suite est une implémentation d’une solution de surveillance de bout en bout destinée à plusieurs ordinateurs distants. Combinant les principaux services Azure pour fournir une implémentation générique du scénario d’entreprise, cette solution peut être utilisée comme point de départ pour votre propre implémentation. Vous pouvez [personnaliser][lnk-customize] la solution pour l’adapter à vos propres besoins professionnels.

Cet article vous familiarise avec les éléments clés de la solution de surveillance à distance pour vous permettre de comprendre son fonctionnement. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels. 
* Concevoir votre propre solution IoT utilisant des services Azure.

## <a name="logical-architecture"></a>Architecture logique
Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![Architecture logique](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Simulations d’appareils
Dans la solution préconfigurée, l’appareil simulé représente un système de refroidissement (un climatiseur dans un bâtiment ou une unité de traitement d’air dans une usine, par exemple). Quand vous déployez la solution préconfigurée, vous configurez automatiquement quatre appareils simulés qui s’exécutent dans un [WebJob Azure][lnk-webjobs]. Les appareils simulés vous permettent d’explorer plus facilement le comportement de la solution sans avoir à déployer des appareils physiques. Pour déployer un appareil physique réel, consultez le didacticiel [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].

Chaque appareil simulé peut envoyer les types de messages suivants à IoT Hub :

| Message | Description |
| --- | --- |
| Démarrage |Lorsque l’appareil démarre, il envoie un message **device-info** contenant des informations sur lui-même au serveur principal. Ce message inclut l’ID de l’appareil, ses métadonnées, une liste des commandes qu’il prend en charge et sa configuration actuelle. |
| Présence |Un appareil envoie régulièrement un message de **présence** afin de signaler s’il détecte la présence d’un capteur. |
| Télémétrie |Un appareil envoie régulièrement un message de **télémétrie** qui indique les valeurs simulées de température et d’humidité, collectées par les capteurs simulés de l’appareil. |

Les appareils simulés envoient les propriétés d’appareil suivantes dans un message **device-info** :

| Propriété | Objectif |
| --- | --- |
| ID de périphérique |Identifiant fourni ou attribué au moment lorsqu’un appareil est créé dans la solution. |
| Fabricant |Fabricant de l'appareil |
| Numéro de modèle |Numéro de modèle de l’appareil |
| Numéro de série |Numéro de série de l’appareil |
| Microprogramme |Version actuelle du microprogramme installé sur l’appareil |
| Plateforme |Architecture de la plate-forme de l’appareil |
| Processeur |Processeur sur lequel est exécuté l’appareil |
| RAM installée |Quantité de RAM installée sur l’appareil |
| État activé du Hub |Propriété d’état IoT Hub de l’appareil |
| Heure de création |Heure à laquelle l’appareil a été créé dans la solution |
| Heure de mise à jour |Heure de dernière mise à jour des propriétés de l’appareil |
| Latitude |Emplacement en latitude de l’appareil |
| Longitude |Emplacement en longitude de l’appareil |

Le simulateur amorce ces propriétés dans les appareils simulés avec des exemples de valeurs.  À chaque fois que le simulateur initialise un appareil simulé, l’appareil publie les métadonnées prédéfinies dans IoT Hub. Notez que cette opération remplace les mises à jour de métadonnées sur le portail des appareils.

Les appareils simulés peuvent prendre en charge les commandes suivantes, envoyées à partir du tableau de bord de la solution via IoT Hub :

| Commande | Description |
| --- | --- |
| PingDevice |Envoie une commande *ping* à l’appareil pour vérifier s’il est actif |
| StartTelemetry |Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry |Commande à l’appareil de cesser l’envoi de données de télémétrie |
| ChangeSetPointTemp |Modifie la valeur nominale autour de laquelle sont générées les données aléatoires |
| DiagnosticTelemetry |Déclenche le simulateur d’appareil pour envoyer une valeur de télémétrie supplémentaire (externalTemp) |
| ChangeDeviceState |Modifie une propriété de l’état étendu de l’appareil et envoie le message d’informations sur l’appareil à partir de l’appareil |

L’accusé de réception de la commande de l’appareil au serveur principal de la solution est fourni via IoT Hub.

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-iothub] ingère les données envoyées par les appareils au cloud et les rend disponibles pour les tâches Azure Stream Analytics (ASA). IoT Hub envoie également des commandes à vos appareils pour le compte du portail de l’appareil. Chaque tâche ASA utilise un groupe de consommateurs IoT Hub distinct pour lire le flux de messages émanant de vos appareils.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Dans la solution de surveillance à distance, [Azure Stream Analytics][lnk-asa] (ASA) distribue les messages d’appareil reçus par IoT Hub à d’autres composants du serveur principal, pour les traiter ou les stocker. Différentes tâches ASA exécutent des fonctions spécifiques en fonction du contenu des messages.

**tâche 1 : informations de l’appareil** filtre les messages d’information de l’appareil provenant du flux de messages entrants et les envoie à un point de terminaison du hub d’événements. Un appareil envoie des messages d’information au démarrage et en réponse à une commande **SendDeviceInfo** . Cette tâche utilise la définition de requête suivante pour identifier les messages **device-info** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Cette tâche envoie ses résultats à un Event Hub pour un traitement ultérieur.

**tâche 2 : règles** compare les valeurs de télémétrie entrantes (température et humidité) aux seuils définis pour chaque appareil. Les valeurs de seuil sont définies dans l’éditeur de règles disponible dans le tableau de bord de la solution. Chaque paire appareil/valeur est stockée par horodatage dans un objet blob que Stream Analytics lit comme des **Données de référence**. La tâche compare toutes les valeurs non vides au seuil défini pour l’appareil. En cas de dépassement de la condition « > », elle génère un événement **d’alarme** indiquant que le seuil a été dépassé et spécifie l’appareil, la valeur ainsi que les valeurs d’horodatage. Cette tâche utilise la définition de requête suivante pour identifier les messages de télémétrie qui doivent déclencher une alarme :

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

La tâche envoie ses résultats à un Event Hub pour un traitement ultérieur et enregistre les détails de chaque alerte dans le stockage d’objets blob à partir duquel le tableau de bord de la solution peut lire les informations d’alerte.

**tâche 3 : télémétrie** agit sur le flux de télémétrie entrant de l’appareil, de deux manières différentes. La première consiste à transférer tous les messages de télémétrie des appareils vers le stockage persistant d’objets blob pour un stockage à long terme. La deuxième calcule les valeurs d’humidité moyenne, minimale et maximale sur une fenêtre glissante de cinq minutes, et envoie ces données au stockage d’objets blob. Le tableau de bord de la solution lit les données de télémétrie à partir du stockage d’objets blob pour remplir les graphiques. Cette tâche utilise la définition de requête suivante :

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
La solution utilise le stockage d’objets blob Azure pour conserver toutes les données de télémétrie brutes et résumées provenant des appareils dans la solution. Le tableau de bord lit les données de télémétrie à partir du stockage d’objets blob pour remplir les graphiques. Pour afficher les alertes, le tableau de bord lit les données du stockage d’objets blob qui enregistre tout dépassement des valeurs de télémétrie par rapport aux valeurs de seuil configurées. La solution utilise également le stockage d’objets blob pour enregistrer les valeurs de seuil définies par un utilisateur dans le tableau de bord.

## <a name="webjobs"></a>WebJobs
Outre les simulateurs d’appareil, les tâches web de la solution hébergent des **processeurs d’événements** exécutés dans une tâche web Azure, qui gère les messages d’information d’appareil et les réponses aux commandes. Il utilise :

* les messages d’informations de l’appareil pour mettre à jour le registre de périphériques (stocké dans la base de données DocumentDB) avec les dernières informations relatives à l’appareil ;
* les messages de réponse aux commandes pour mettre à jour l’historique des commandes de l’appareil (stocké dans la base de données DocumentDB).

## <a name="documentdb"></a>Base de données de documents
La solution utilise une base de données DocumentDB pour stocker des informations sur les appareils connectés à la solution. Ces informations comprennent des métadonnées d’appareil et l’historique des commandes envoyées aux appareils à partir du tableau de bord.

## <a name="web-apps"></a>les applications web
### <a name="remote-monitoring-dashboard"></a>Tableau de bord de surveillance à distance
Cette page de l’application web utilise les contrôles Javascript PowerBI (consultez le [référentiel d’éléments visuels PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) pour visualiser les données de télémétrie provenant des appareils. La solution utilise la tâche de télémétrie ASA pour écrire les données de télémétrie dans le stockage d’objets blob.

### <a name="device-administration-portal"></a>Portail d’administration des appareils
Cette application Web vous permet de :

* Configurer un nouvel appareil. Cela vous permet de définir l’identifiant unique de l’appareil et de générer la clé d’authentification. Les informations relatives à l’appareil sont inscrites à la fois dans le registre d’identités IoT Hub et dans la base de données DocumentDB de la solution.
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



<!--HONumber=Nov16_HO3-->


