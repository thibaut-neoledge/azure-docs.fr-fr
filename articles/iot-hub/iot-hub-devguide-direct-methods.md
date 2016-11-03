<properties
 pageTitle="Guide du développeur - Méthodes directes | Microsoft Azure"
 description="Guide pour développeur IoT Hub Azure - Utiliser des méthodes directes pour appeler du code sur vos appareils"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>


# <a name="invoke-a-direct-method-on-a-device-preview"></a>Appeler une méthode directe sur un appareil (version préliminaire)

## <a name="overview"></a>Vue d'ensemble

IoT Hub vous donne la possibilité d’appeler des méthodes sur des appareils à partir du cloud. Les méthodes représentent une interaction de demande-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur) pour permettre à l’utilisateur de connaître l’état de l’appel. Cela est utile pour les scénarios où la conduite à suivre immédiate diffère selon que l’appareil a ou non pu répondre, comme l’envoi d’un SMS d’activation à un appareil quand celui-ci est hors connexion (un SMS étant plus onéreux qu’un appel de méthode).

Une méthode peut être considérée comme un appel de procédure distante directement adressé à l’appareil. Seules des méthodes implémentées sur un appareil peuvent être appelées à partir du cloud. Si le cloud tente d’appeler une méthode sur un appareil ne disposant pas de cette méthode définie, l’appel de méthode échoue.

Chaque méthode d’appareil cible à un seul appareil. Les [tâches][lnk-devguide-jobs] offrent un moyen d’appeler des méthodes sur plusieurs appareils, et de mettre en file d’attente l’appel de méthode pour les appareils déconnectés.

Toute personne disposant d’autorisations **Connexion de service** sur IoT Hub peut appeler une méthode sur un appareil.

### <a name="when-to-use"></a>Quand utiliser

Les méthodes d’appareil ressemblent aux [messages cloud-à-appareil][lnk-devguide-messages] en ce qu’elles permettent au serveur principal cloud de transmettre des informations à un appareil, mais elles présentent également des différences fondamentales. Conceptuellement, les méthodes sont synchrones et non durables, tandis que les messages cloud-à-appareil sont asynchrones et offrent une durabilité jusqu’à 48 heures.

Méthodes suivent un modèle de demande-réponse et ne sont pas durables. L’absence de durabilité offre deux avantages immédiats lors de l’envoi de commandes à des appareils :

- Le **retour immédiat sur l’exécution de la méthode** signifie il n’est pas nécessaire de gérer la corrélation entre la demande et la réponse.
- Le **débit plus élevé** signifie que les opérations peuvent être effectuées plus rapidement car IoT Hub n’offre aucune durabilité. IoT Hub permet d’effectuer davantage d’appels de méthode par unité que les messages cloud-à-appareil.

Les messages cloud-à-appareil ne sont pas nécessairement des commandes adressées à un appareil. Il constituent plutôt un service cloud transmettant des éléments d’informations à un appareil qui les récupère à son gré et peut y répondre ou non. Les messages cloud-à-appareil ont un délai d’expiration plus long (jusqu’à 48 heures), alors que les méthodes expirent beaucoup plus rapidement.

Utilisez des méthodes d’appareil pour effectuer des appels de commande immédiate sur un appareil, et des travaux pour un appel planifié de commande sur un appareil.

## <a name="method-lifecycle"></a>Cycle de vie de méthode

Les méthodes sont implémentées sur l’appareil et peuvent nécessiter de zéro à plusieurs entrées dans la charge utile pour s’instancier correctement. Vous appelez une méthode directe via un URI côté service (`{iot hub}/twins/{device id}/methods/`). Un appareil reçoit des méthodes directes via une rubrique MQTT spécifique de l’appareil (`$iothub/methods/POST/{method name}/`). Nous pourrions prendre en charge des méthodes sur des protocoles réseau côté appareil supplémentaires à l’avenir.

> [AZURE.NOTE] Lorsque vous appelez une méthode directe sur un appareil, les noms et valeurs de propriété peuvent contenir uniquement des caractères alphanumériques US-ASCII imprimables, à l’exception des caractères suivants : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Les méthodes sont synchrones et réussissent ou échouent à l’issue du délai d’expiration (par défaut, 30 secondes, extensible à 3 600 secondes). Les méthodes sont utiles dans des scénarios interactifs où vous souhaitez qu’un appareil agisse si et seulement s’il est en ligne et reçoit des commandes, telles que l’allumage d’une lumière, en provenance d’un téléphone. Dans ces scénarios, vous souhaitez constater immédiatement la réussite ou l’échec de la commande, de façon à ce que le service cloud puisse agir sur le résultat dès que possible. L’appareil peut renvoyer un corps de message résultant de la méthode, mais il n’est pas obligatoire que la méthode procède de la sorte. Il existe ni garantie de classement, ni sémantique de concurrence sur les appels de méthode.

Les appels de méthode d’appareil sont exclusivement HTTP côté cloud et exclusivement MQTT côté appareil.

## <a name="reference"></a>Référence

### <a name="servicefacing"></a>Côté service

#### <a name="method-invocation"></a>Appel de méthode

Les appels de méthode directe sur un appareil sont des appels HTTP qui comprennent les éléments suivants :

- l’*URI* spécifique de l’appareil (`{iot hub}/twins/{device id}/methods/`) ;
- la *méthode* POST ;
- des *en-têtes* contenant l’autorisation, l’ID de demande, le type de contenu et l’encodage du contenu ;
- un *corps* JSON transparent au format suivant :

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Le délai d’attente est exprimé en secondes. Si le délai d’attente n’est pas défini, sa valeur par défaut est de 30 secondes.
  
#### <a name="response"></a>Réponse

Le serveur principal reçoit une réponse qui comprend les éléments suivants :

- un *code d’état HTTP*, qui est utilisé pour des erreurs en provenance d’IoT Hub, dont l’erreur 404 pour les appareils non connectés ;
- des *en-têtes* contenant l’ETag, l’ID de demande, le type de contenu et l’encodage du contenu ;
- un *corps* JSON au format suivant :

```
{
    "status" : "OK",
    "payload" : {...}
}
```
  
   Les propriétés `status` et `body` sont fournies par l’appareil et permettent de répondre avec le code d’état et/ou la description spécifiques de l’appareil.

### <a name="devicefacing"></a>Côté appareil

#### <a name="method-invocation"></a>Appel de méthode

Les appareils reçoivent des demandes de méthode directe sur la rubrique MQTT : `$iothub/methods/POST/{method name}/?$rid={request id}`

Le corps que l’appareil reçoit est au format suivant :

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Les demandes de méthode sont QoS 0.

#### <a name="response"></a>Réponse

L’appareil envoie des réponses à `$iothub/methods/res/{status}/?$rid={request id}`, où :

 - La propriété `status` est l’état d’exécution de la méthode fourni par l’appareil.
 - La propriété `$rid`est l’ID de demande de l’appel de méthode reçu d’IoT Hub.

Le corps est défini par l’appareil et peut être n’importe quel état.

### <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Autres rubriques de référence dans le Guide du développeur :

- La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
- La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre lors de l’utilisation du service.
- La rubrique [Kits de développement logiciel (SDK) de services et appareils IoT Hub][lnk-sdks] répertorie les kits de développement logiciel en différents langages que vous pouvez utiliser lors du développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
- La rubrique [Langage de requête pour les représentations, les méthodes et les travaux][lnk-query] décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur des représentations d’appareil, des méthodes et des travaux.
- La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment utiliser les méthodes directes, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur :

- [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

- [Utiliser des méthodes cloud-à-appareil][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


