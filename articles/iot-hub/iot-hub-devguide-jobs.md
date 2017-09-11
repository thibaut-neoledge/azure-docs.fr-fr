---
title: "Présentation des tâches Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Planification des travaux à exécuter sur plusieurs appareils connectés à votre IoT Hub. Les tâches peuvent mettre à jour les balises et les propriétés souhaitées, et appeler des méthodes directes sur plusieurs appareils."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: juanpere
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 6e4ca8ad0c444930f5e45eed0a024412de82dbb1
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="schedule-jobs-on-multiple-devices"></a>Planifier des travaux sur plusieurs appareils
## <a name="overview"></a>Vue d'ensemble
Comme décrit dans les articles précédents, Azure IoT Hub utilise un certain nombre de composantes ([balises et propriétés de jumeau d’appareil][lnk-twin-devguide], et [méthodes directes][lnk-dev-methods]).  Généralement, les applications backend permettent aux administrateurs et opérateurs d’appareil de mettre à jour et d’interagir avec les appareils IoT par lots et à une heure planifiée.  Les travaux englobent l’exécution des mises à jour des jumeaux d’appareil et des méthodes directes sur un ensemble d’appareils à une heure planifiée.  Par exemple, un opérateur peut utiliser une application backend qui lance et suit un travail pour le redémarrage d’un ensemble d’appareils dans le bâtiment 43 à l’étage 3 à une heure qui ne perturbera pas les opérations du bâtiment.

### <a name="when-to-use"></a>Quand utiliser
Envisagez d’utiliser les travaux quand le backend d’une solution doit planifier et suivre la progression des activités suivantes sur un ensemble d’appareils :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

## <a name="job-lifecycle"></a>Cycle de vie de tâche
Les travaux sont lancés par l’application backend de la solution et maintenus par IoT Hub.  Vous pouvez lancer un travail via une URI de service (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) et vérifier la progression d’un travail en cours via une URI de service (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`).  Une fois qu’un travail est initialisé, la vérification des travaux permet à l’application backend d’actualiser l’état des tâches en cours d’exécution.

> [!NOTE]
> Lorsque vous lancez une tâche, les noms et valeurs de propriété peuvent contenir uniquement des caractères alphanumériques US-ASCII imprimables, à l’exception des caractères suivants : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

## <a name="reference-topics"></a>Rubriques de référence :
Les rubriques de référence suivantes vous fournissent des informations supplémentaires sur l’utilisation des travaux.

## <a name="jobs-to-execute-direct-methods"></a>Tâches pour exécuter des méthodes directes
Voici les détails de la requête HTTP 1.1 pour exécuter une [méthode directe][lnk-dev-methods] sur un ensemble d’appareils en utilisant un travail :

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
La condition de requête peut également être un ID d’appareil unique ou figurer sur une liste d’ID comme illustré ci-dessous

**Exemples**
```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
Le [langage de requête IoT Hub][lnk-query] couvre le langage de requête IoT Hub plus en détail.

## <a name="jobs-to-update-device-twin-properties"></a>Travaux pour mettre à jour les propriétés d’un jumeau d’appareil
Voici les détails de la requête HTTP 1.1 pour mettre à jour les propriétés d’un jumeau d’appareil à l’aide d’un travail :

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Vérification de la progression des travaux
Voici les détails de la requête HTTP 1.1 pour [interroger des travaux][lnk-query] :

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```

Le paramètre continuationToken est fourni dans la réponse.  

## <a name="jobs-properties"></a>Propriétés des travaux
Voici une liste de propriétés et de descriptions correspondantes qui peuvent être utilisées lors de la vérification de travaux ou des résultats de travaux.

| Propriété | Description |
| --- | --- |
| **jobId** |L’application a fourni un ID pour le travail. |
| **startTime** |L’application a fourni l’heure de début (ISO-8601) pour le travail. |
| **endTime** |IoT Hub a fourni la date (ISO-8601) de fin du travail. Valide uniquement lorsque la tâche atteint l’état « terminé ». |
| **type** |Types de tâches : |
| **scheduledUpdateTwin** : travail permettant de mettre à jour un ensemble de propriétés souhaitées ou de balises. | |
| **scheduledDeviceMethod** : travail permettant d’appeler une méthode d’appareil sur un ensemble de jumeaux d’appareil. | |
| **statut** |État actuel du travail. Valeurs possibles pour l'état : |
| **pending** : planifié et en attente de récupération par le service du travail. | |
| **scheduled** : planifié pour une date ultérieure. | |
| **running** : le travail est actuellement actif. | |
| **cancelled** : le travail a été annulé. | |
| **failed** : échec de la tâche. | |
| **completed** : le travail est terminé..</seg> | |
| **deviceJobStatistics** |Statistiques relatives à l’exécution du travail. |

Propriétés **deviceJobStatistics**.

| Propriété | Description |
| --- | --- |
| **deviceJobStatistics.deviceCount** |Nombre d’appareils du travail. |
| **deviceJobStatistics.failedCount** |Nombre d’appareils sur lesquels le travail a échoué. |
| **deviceJobStatistics.succeededCount** |Nombre d’appareils sur lesquels le travail a réussi. |
| **deviceJobStatistics.runningCount** |Nombre d’appareils qui exécutent actuellement le travail. |
| **deviceJobStatistics.pendingCount** |Nombre d’appareils en attente d’exécution du travail. |

### <a name="additional-reference-material"></a>Matériel de référence supplémentaire
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre en cas d’utilisation du service.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* L’article [Langage de requête d’IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-query] décrit le langage de requête d’IoT Hub permettant de récupérer, à partir d’IoT Hub, des informations relatives à vos jumeaux d’appareil et à vos travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

* [Planifier et diffuser des travaux][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md

