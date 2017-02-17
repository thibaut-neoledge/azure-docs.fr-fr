---
title: "Présentation des méthodes directes Azure IoT Hub | Microsoft Docs"
description: "Guide de développeur - Utiliser des méthodes directes pour appeler du code sur vos appareils à partir d’une application de service."
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 9c2817129162ab17faadf3c5ecf8ef7dcb370c3c
ms.openlocfilehash: 2c9c4b59077ea7d31677a5e1c690160bf63633a6


---
# <a name="direct-methods"></a>Méthodes directes
## <a name="overview"></a>Vue d'ensemble
IoT Hub vous donne la possibilité d’appeler des méthodes directes sur des appareils à partir du cloud. Les méthodes directes représentent une interaction de demande-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur). Cela est utile pour les scénarios où la conduite à suivre immédiate diffère selon que l’appareil a ou non pu répondre, comme l’envoi d’un SMS d’activation à un appareil quand celui-ci est hors connexion (un SMS étant plus onéreux qu’un appel de méthode).

Chaque méthode d’appareil cible à un seul appareil. Les [travaux][lnk-devguide-jobs] offrent un moyen d’appeler des méthodes directes sur plusieurs appareils, et de planifier un appel de méthode pour des appareils déconnectés.

Toute personne disposant d’autorisations **Connexion de service** sur IoT Hub peut appeler une méthode sur un appareil.

### <a name="when-to-use"></a>Quand utiliser
Les méthodes directes suivent un modèle de requête-réponse et sont destinées aux communications qui nécessitent une confirmation immédiate de leur résultat, généralement le contrôle interactif de l’appareil, par exemple, pour activer un ventilateur.

En cas de doute entre l’utilisation des propriétés souhaitées des méthodes directes ou des messages cloud-à-appareil, voir [Instructions pour la communication appareil-à-cloud][lnk-c2d-guidance].

## <a name="method-lifecycle"></a>Cycle de vie de méthode
Les méthodes directes sont implémentées sur l’appareil et peuvent nécessiter de zéro à plusieurs entrées dans la charge utile pour s’instancier correctement. Vous appelez une méthode directe via un URI côté service (`{iot hub}/twins/{device id}/methods/`). Un appareil reçoit des méthodes directes via une rubrique MQTT spécifique de l’appareil (`$iothub/methods/POST/{method name}/`). Nous pourrions prendre en charge des méthodes directes sur des protocoles réseau côté appareil supplémentaires à l’avenir.

> [!NOTE]
> Lorsque vous appelez une méthode directe sur un appareil, les noms et valeurs de propriété peuvent contenir uniquement des caractères alphanumériques US-ASCII imprimables, à l’exception des caractères suivants : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Les méthodes directes sont synchrones et réussissent ou échouent à l’issue du délai d’expiration (par défaut, 30 secondes, extensible à 3 600 secondes). Les méthodes directes sont utiles dans des scénarios interactifs où vous souhaitez qu’un appareil agisse si et seulement s’il est en ligne et reçoit des commandes, telles que l’allumage d’une lumière, en provenance d’un téléphone. Dans ces scénarios, vous souhaitez constater immédiatement la réussite ou l’échec de la commande, de façon à ce que le service cloud puisse agir sur le résultat dès que possible. L’appareil peut renvoyer un corps de message résultant de la méthode, mais il n’est pas obligatoire que la méthode procède de la sorte. Il existe ni garantie de classement, ni sémantique de concurrence sur les appels de méthode.

Les méthodes directes sont exclusivement HTTP côté cloud, et exclusivement MQTT côté appareil.

La charge utile pour les requêtes et les réponses de méthode correspond à un document JSON d’une taille pouvant aller jusqu’à 8 Ko.

## <a name="reference-topics"></a>Rubriques de référence :
Les rubriques de référence suivantes vous fournissent des informations supplémentaires sur l’utilisation des méthodes directes.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Appeler une méthode directe à partir d’une application principale
### <a name="method-invocation"></a>Appel de méthode
Les appels de méthode directe sur un appareil sont des appels HTTP qui comprennent les éléments suivants :

* l’*URI* spécifique de l’appareil (`{iot hub}/twins/{device id}/methods/`) ;
* la *méthode* POST ;
* des *en-têtes* contenant l’autorisation, l’ID de demande, le type de contenu et l’encodage du contenu ;
* un *corps* JSON transparent au format suivant :

```
{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

Le délai d’attente est exprimé en secondes. Si le délai d’attente n’est pas défini, sa valeur par défaut est de 30 secondes.

### <a name="response"></a>Réponse
L’application principale reçoit une réponse qui comprend les éléments suivants :

* un *code d’état HTTP*, qui est utilisé pour des erreurs en provenance d’IoT Hub, dont l’erreur 404 pour les appareils non connectés ;
* des *en-têtes* contenant l’ETag, l’ID de demande, le type de contenu et l’encodage du contenu ;
* un *corps* JSON au format suivant :

```
{
    "status" : 201,
    "payload" : {...}
}
```

   Les propriétés `status` et `body` sont fournies par l’appareil et permettent de répondre avec le code d’état et/ou la description spécifiques de l’appareil.

## <a name="handle-a-direct-method-on-a-device"></a>Gérer une méthode directe sur un appareil
### <a name="method-invocation"></a>Appel de méthode
Les appareils reçoivent des demandes de méthode directe sur la rubrique MQTT : `$iothub/methods/POST/{method name}/?$rid={request id}`

Le corps que l’appareil reçoit est au format suivant :

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Les demandes de méthode sont QoS 0.

### <a name="response"></a>Réponse
L’appareil envoie des réponses à `$iothub/methods/res/{status}/?$rid={request id}`, où :

* La propriété `status` est l’état d’exécution de la méthode fourni par l’appareil.
* La propriété `$rid`est l’ID de demande de l’appel de méthode reçu d’IoT Hub.

Le corps est défini par l’appareil et peut être n’importe quel état.

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre en cas d’utilisation du service.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser lors du développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* La rubrique [Langage de requête d’IoT Hub pour les représentations d’appareil et les travaux][lnk-query] décrit le langage de requête d’IoT Hub permettant de récupérer à partir d’IoT Hub des informations sur des représentations d’appareil et des travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez comment utiliser les méthodes directes, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur IoT Hub :

* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

* [Utiliser des méthodes directes][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md



<!--HONumber=Jan17_HO4-->


