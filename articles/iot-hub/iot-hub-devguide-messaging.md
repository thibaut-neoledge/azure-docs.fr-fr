---
title: Guide du développeur - Messagerie | Microsoft Docs
description: Guide du développeur Azure IoT Hub - Messagerie d’appareil-à-cloud et de cloud-à-appareil
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett

---
# <a name="send-and-receive-messages-with-iot-hub"></a>Envoyer et recevoir des messages avec IoT Hub
## <a name="overview"></a>Vue d'ensemble
IoT Hub fournit les primitives de messagerie suivantes pour communiquer avec un appareil :

* [Appareil-à-cloud][lnk-d2c] : à partir d’un appareil vers un serveur principal d’applications.
* [Cloud-à-appareil][lnk-c2d]: à partir d’un serveur principal d’applications (*service* ou *cloud*).

Les principales propriétés de la fonctionnalité de messagerie IoT Hub sont la fiabilité et la durabilité des messages. Ces propriétés activent la résilience de la connectivité intermittente côté appareils et des pics de chargement dans le traitement d’événements côté cloud. IoT Hub implémente *au moins une fois* des garanties de remise pour l’envoi de messages appareil vers cloud et cloud vers appareil.

IoT Hub prend en charge plusieurs [protocoles d’appareil][lnk-protocols] (par exemple, MQTT, AMQP et HTTP). Pour assurer une interopérabilité fluide entre les différents protocoles, IoT Hub définit un [format de message commun][lnk-message-format] que tous les protocoles côté appareil prennent en charge.

IoT Hub expose un [point de terminaison compatible avec Event Hubs][lnk-compatible-endpoint] pour permettre aux applications de serveur principal de lire les messages appareil-à-cloud que le concentrateur reçoit.

### <a name="when-to-use"></a>Quand utiliser
La messagerie est une capacité fondamentale d’IoT Hub. Utilisez-la chaque fois que vous avez besoin envoyer des messages de votre appareil à votre serveur principal, ou de votre serveur principal à un appareil.

Pour une comparaison des services IoT Hub et Event Hubs, voir [Comparaison entre IoT Hub et Event Hubs][lnk-compare].

## <a name="devicetocloud-messages"></a>Messages Appareil vers cloud
Vous envoyez des messages appareil-à-cloud via un point de terminaison côté appareil (**/devices/{deviceId}/messages/events**). Votre service principal reçoit des messages appareil-à-cloud via un point de terminaison côté service web (**/messages/events**) qui est compatible avec [Event Hubs][lnk-event-hubs]. Par conséquent, vous pouvez utiliser une [intégration et des Kits de développement logiciel (SDK) Event Hubs][lnk-compatible-endpoint] standard pour recevoir des messages appareil-à-cloud.

IoT Hub implémente la messagerie appareil-à-cloud d’une manière similaire à [Event Hubs][lnk-event-hubs]. Les messages appareil-à-cloud d’IoT Hub ressemblent davantage à des *événements* Event Hubs qu’à des *messages* [Service Bus][lnk-servicebus].

Les conséquences sont les suivantes :

* À l’instar des événements Event Hubs, les messages appareil-à-cloud sont durables et conservés dans IoT Hub jusqu’à sept jours (voir [Options de configuration appareil-à-cloud][lnk-d2c-configuration]).
* Les messages appareil-à-cloud sont partitionnés en un ensemble fixe de partitions, qui est défini au moment de la création (voir [Options de configuration appareil-à-cloud][lnk-d2c-configuration]).
* Comme pour Event Hubs, les clients lisant des messages appareil-à-cloud doivent gérer des partitions et des points de contrôle. Voir [Event Hubs - Événements de consommation][lnk-event-hubs-consuming-events].
* Comment les événements Event Hubs, les messages appareil-à-cloud ne doivent pas dépasser 256 Ko, et peuvent groupés en lots pour optimiser les envois. Les lots doivent représenter 256 Ko de volume et 500 messages maximum.

Toutefois, il existe quelques différences importantes entre les messages appareil vers cloud IoT Hub et Event Hubs :

* Comme expliqué dans la section [Contrôler l’accès à IoT Hub][lnk-devguide-security], IoT Hub permet l’authentification et le contrôle d’accès par appareil.
* IoT Hub permet de connecter des millions d’appareils simultanément (voir [Quotas et limitation][lnk-quotas]), tandis qu’Event Hubs est limité à 5 000 connexions AMQP par espace de noms.
* IoT Hub n’autorise pas le partitionnement arbitraire à l’aide d’une **PartitionKey**. Les messages appareil vers cloud sont partitionnés selon leur **deviceId**d’origine.
* La mise à l’échelle IoT Hub est légèrement différente de celle d’Event Hubs. Pour plus d’informations, voir [Mise à l’échelle d’IoT Hub][lnk-guidance-scale].

> [!NOTE]
> Vous ne pouvez pas substituer IoT Hub à Event Hubs dans tous les scénarios. Par exemple, dans certains calculs de traitement d’événements, il peut être nécessaire de repartitionner les événements par rapport à une autre propriété ou à un autre champ avant d’analyser les flux de données. Dans ce cas, vous pouvez utiliser un hub d’événements pour dissocier les deux parties du pipeline de traitement de flux. Pour plus d’informations, voir *Partitions* dans [Vue d’ensemble d’Azure Event Hubs][lnk-eventhub-partitions].
> 
> 

Pour plus d’informations sur la façon d’utiliser la messagerie appareil-à-cloud, voir [API et kits de développement logiciel (SDK) IoT Hub][lnk-sdks].

> [!NOTE]
> Si vous utilisez HTTP pour envoyer des messages appareil vers cloud, les valeurs et les noms de propriétés ne peuvent contenir que des caractères alphanumériques ASCII plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="nontelemetry-traffic"></a>Trafic autre que la télémétrie
Souvent, outre les points de données de télémétrie, les appareils envoient également des messages et demandes qui nécessitent une exécution et une gestion au niveau de la couche de logique métier d’application. Il s’agit, par exemple, des alertes critiques qui doivent déclencher une action spécifique au niveau du serveur principal, ou encore des réponses de l’appareil aux commandes envoyées par le serveur principal.

Pour plus d’informations sur la meilleure façon de traiter ce type de message, voir [Didacticiel : traiter les messages des appareils vers le cloud IoT Hub][lnk-d2c-tutorial].

### <a name="devicetocloud-configuration-options"></a>Options de configuration appareil-à-cloud
Un hub IoT expose les propriétés suivantes pour vous permettre de contrôler les messages appareil-à-cloud.

* **Nombre de partitions**. Configurez cette propriété lors de la création pour définir le nombre de partitions pour la réception d’événements appareil-à-cloud.
* **Durée de rétention**. Cette propriété spécifie la durée de rétention des messages appareil-à-cloud. La durée par défaut est de un jour, mais elle peut être augmentée à sept jours.

À l’instar d’Event Hubs, IoT Hub vous permet aussi de gérer des groupes de consommateurs sur le point de terminaison de la réception de messages appareil vers cloud.

Toutes les propriétés ci-dessus peuvent être modifiées par programme via [Azure IoT Hub - API de fournisseur de ressources][lnk-resource-provider-apis], ou via le [portail Azure][lnk-management-portal].

### <a name="antispoofing-properties"></a>Propriétés de détection d’usurpation d’identité
Pour éviter l’usurpation d’appareil dans les messages appareil-à-cloud, IoT Hub marque tous les messages avec les propriétés suivantes :

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Les deux premières propriétés contiennent les ID **deviceId** et **generationId** de l’appareil d’origine, conformément aux [Propriétés d’identité des appareils][lnk-device-properties].

La propriété **ConnectionAuthMethod** contient un objet sérialisé JSON avec les propriétés suivantes :

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloudtodevice-messages"></a>Messages Cloud vers appareil
Vous envoyez les messages cloud-à-appareil via un point de terminaison côté service (**/messages/devicebound**). Un appareil les reçoit via un point de terminaison spécifique de l’appareil (**/devices/{deviceId}/messages/devicebound**).

Chaque message cloud-à-appareil cible un seul appareil en définissant la propriété **to** sur **/devices/{deviceId}/messages/devicebound**.

> [!IMPORTANT]
> Chaque file d’attente d’appareil peut contenir jusqu’à 50 messages cloud-à-appareil. Les tentatives d’envoi d’un nombre supérieur de messages au même appareil entraînent une erreur.
> 
> [!NOTE]
> Quand vous envoyez des messages cloud-à-appareil, les valeurs et les noms de propriétés peuvent contenir uniquement des caractères alphanumériques ASCII, plus les caractères ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="message-lifecycle"></a>Cycle de vie des messages
Pour garantir la remise au moins une fois des messages, IoT Hub conserve les messages cloud-à-appareil dans des files d’attente par appareil. Les appareils doivent explicitement confirmer l’ *achèvement* afin que IoT Hub supprime les messages de la file d’attente. Cela garantit la résilience contre les échecs de connectivité et d’appareils.

L’illustration suivante présente le graphique d’état du cycle de vie d’un message cloud-à-appareil.

![Cycle de vie des messages cloud-à-appareil][img-lifecycle]

Quand le service envoie un message, celui-ci est considéré comme *Enqueued*(En file d’attente). Quand un appareil veut *recevoir* un message, IoT Hub *verrouille* le message (définit son état sur **Invisible**) pour autoriser d’autres threads sur le même appareil à commencer à recevoir d’autres messages. Quand un thread d’appareil a fini de traiter un message, il notifie IoT Hub en *terminant* le message.

Un appareil peut également :

* *rejeter* le message, ce qui amène IoT Hub à lui attribuer l’état **Deadlettered** (Lettre morte) ; Remarque : les appareils se connectant avec MQTT ne peuvent pas rejeter des messages C2D.
* *abandonner* le message, ce qui amène IoT Hub à replacer le message dans la file d’attente avec l’état **Enqueued**(En file attente).

Il est possible qu’un thread ne parvienne pas à traiter un message sans en avertir IoT Hub. Dans ce cas, les messages passent automatiquement de l’état **Invisible** à l’état **Enqueued** (En file d’attente) après un *délai d’attente de visibilité (ou de verrouillage)*. La valeur par défaut de ce délai est d’une minute.

Un message peut passer de l’état **Enqueued** (En file d’attente) à l’état **Invisible** et inversement, au maximum le nombre de fois spécifié dans la propriété **Nombre maximal de remises** sur IoT Hub. Une fois ce nombre de transitions atteint, IoT Hub attribue au message l’état **Deadlettered**(Lettre morte). De même, IoT Hub attribue à un message l’état **Deadlettered** (Lettre morte) à l’issue de son délai d’expiration (voir [Durée de vie][lnk-ttl]).

Pour accéder à des instructions sur les messages cloud-à-appareil, voir [Didacticiel : envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d-tutorial]. Pour des rubriques de référence à propos des différences de présentation de la fonctionnalité cloud-à-appareil entre les API et les SDK, voir [API et Kits de développement logiciel (SDK) IoT Hub][lnk-sdks].

> [!NOTE]
> Généralement, les messages cloud-à-appareil sont achevés à chaque fois que la perte du message n’affecte pas la logique d’application. Par exemple, le contenu du message a bien été conservé dans le stockage local ou une opération a été exécutée avec succès. Le message peut également transporter des informations temporaires, dont la perte n’aurait aucun impact sur les fonctionnalités de l’application. Parfois, pour les tâches longues, vous pouvez terminer le message cloud vers appareil après la conservation de la description de la tâche dans le stockage local. Vous pouvez ensuite notifier le serveur principal d’applications à l’aide d’un ou de plusieurs messages appareil vers cloud à différents stades de la progression de la tâche.
> 
> 

### <a name="message-expiration-time-to-live"></a>Expiration du message (durée de vie)
Chaque message cloud-à-appareil est doté d’un délai d’expiration. Cette durée est définie soit par le service (dans la propriété **ExpiryTimeUtc** ), soit par IoT Hub avec la *durée de vie* par défaut spécifiée en tant que propriété IoT Hub. Voir [Options de configuration cloud-à-appareil ][lnk-c2d-configuration].

> [!NOTE]
> Un moyen courant de tirer parti de l’expiration du message et d’éviter l’envoi de messages à des appareils déconnectés consiste à définir des valeurs de durée de vie courtes. Cette approche produit le même résultat que la gestion de l’état de connexion de l’appareil, tout en étant plus efficace. Lorsque vous demandez des accusés de réception des messages, IoT Hub vous informe sur les appareils en mesure de recevoir des messages et ceux qui ne sont pas en ligne ou qui sont en état d’échec.
> 
> 

### <a name="message-feedback"></a>Commentaires de messages
Lorsque vous envoyez un message cloud-à-appareil, le service peut demander la remise d’un commentaire de message concernant l’état final de ce message.

* Si vous définissez la propriété **Ack** sur **positive**, IoT Hub génère un message de commentaire si et seulement si le message cloud-à-appareil est à l’état **Completed** (Terminé).
* Si vous définissez la propriété **Ack** sur **negative**, IoT Hub génère un message de commentaire si et seulement si le message cloud-à-appareil est à l’état **Deadlettered** (Lettre morte).
* Si vous définissez la propriété **Ack** sur **full**, IoT Hub génère un message de commentaire dans les deux cas.

> [!NOTE]
> Si la propriété **Ack** est définie sur **full** et que vous ne recevez pas de message de commentaire, cela signifie que le message de commentaire a expiré. Le service ne peut pas savoir ce qui est arrivé au message d’origine. Dans la pratique, un service doit s'assurer qu'il peut traiter les commentaires avant leur expiration. Le délai d'expiration maximal est de deux jours, ce qui vous laisse suffisamment de temps pour rendre le service opérationnel en cas de défaillance.
> 
> 

Comme expliqué dans la section [Points de terminaison][lnk-endpoints], IoT Hub fournit des commentaires sous la forme de messages via un point de terminaison côté service (**/messages/servicebound/feedback**). La sémantique de réception des commentaires est identique à celle des messages cloud-à-appareil, et présente le même [cycle de vie des messages][lnk-lifecycle]. Chaque fois que c’est possible, des commentaires de messages sont mis en lot dans un seul message, au format suivant :

| Propriété | Description |
| --- | --- |
| EnqueuedTime |Horodatage indiquant la date et l’heure de création du message. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

Le corps est un tableau sérialisé JSON des enregistrements, chacun disposant des propriétés suivantes :

| Propriété | Description |
| --- | --- |
| EnqueuedTimeUtc |Horodatage indiquant la date et l’heure du résultat du message. Par exemple, l’achèvement de l’appareil ou l’expiration du message. |
| OriginalMessageId |**MessageId** du message Cloud vers appareil auquel appartiennent ces informations de commentaires. |
| StatusCode |Entier obligatoire. Utilisé dans les messages de commentaires générés par IoT Hub. <br/> 0 = réussite <br/> 1 = message arrivé à expiration <br/> 2 = nombre maximal de remises dépassé <br/>  3 = message rejeté |
| Description |Valeurs de chaîne pour **StatusCode**. |
| deviceId |**DeviceId** de l’appareil cible du message Cloud vers appareil auquel appartient ce commentaire. |
| DeviceGenerationId |**DeviceGenerationId** de l’appareil cible du message Cloud vers appareil auquel appartient ce commentaire. |

> [!IMPORTANT]
> Le service doit spécifier un **MessageId** pour le message cloud vers appareil afin de pouvoir mettre en corrélation ses commentaires avec le message d’origine.
> 
> 

L’exemple suivant montre le corps d’un message de commentaire.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloudtodevice-configuration-options"></a>Options de configuration cloud-à-appareil
Chaque IoT Hub expose les options de configuration suivantes pour la messagerie Cloud vers appareil.

| Propriété | Description | Plage et valeur par défaut |
| --- | --- | --- |
| defaultTtlAsIso8601 |Durée de vie par défaut pour les messages cloud-à-appareil. |Intervalle ISO_8601 jusqu’à 2D (minimum 1 minute). Par défaut : 1 heure. |
| maxDeliveryCount |Nombre de remises maximal pour les files d’attente par appareil cloud-à-appareil |1 à 100. Par défaut : 10. |
| feedback.ttlAsIso8601 |Rétention des messages de commentaires liés au service. |Intervalle ISO_8601 jusqu’à 2D (minimum 1 minute). Par défaut : 1 heure. |
| feedback.maxDeliveryCount |Nombre de remises maximal pour la file d’attente de commentaires. |1 à 100. Par défaut : 100. |

Pour plus d’informations, voir [créer des IoT Hubs][lnk-portal].

## <a name="read-devicetocloud-messages"></a>Lires les messages appareil-à-cloud
IoT Hub expose un point de terminaison pour permettre aux services de votre serveur principal de lire les messages appareil-à-cloud que reçoit votre concentrateur. Le point de terminaison est compatible avec Event Hubs, ce qui vous permet d’utiliser tout mécanisme pris en charge par le service Event Hubs pour la lecture des messages.

Lorsque vous utilisez le [Kit de développement logiciel (SDK) Azure Service Bus pour .NET][lnk-servicebus-sdk] ou l’[hôte du processeur d’événements Event Hubs][lnk-eventprocessorhost], vous pouvez utiliser toute chaîne de connexion IoT Hub avec les autorisations appropriées. Vous utilisez ensuite **messages/événements** comme nom d’Event Hub.

Lorsque vous utilisez des Kits de développement logiciel (SDK) ou des intégrations de produits qui n’ont pas connaissance d’IoT Hub, vous devez récupérer un point de terminaison compatible avec Event Hubs et un nom de hub d’événements à partir des paramètres IoT Hub dans le [portail Azure][lnk-management-portal] :

1. Dans le panneau IoT Hub, cliquez sur **Messagerie**.
2. Dans la section **Device-to-cloud settings** (Paramètres appareil-à-cloud), vous trouverez les valeurs suivantes : **Point de terminaison compatible avec les hubs d’événements**, **Nom compatible avec les hubs d’événements** et **Partitions**.
   
    ![Paramètres Appareil vers cloud][img-eventhubcompatible]

> [!NOTE]
> Si le Kit de développement logiciel (SDK) requiert une valeur **Nom d’hôte** ou **Espace de noms**, supprimez le modèle du **Point de terminaison compatible avec les hubs d’événements**. Par exemple, si votre point de terminaison compatible avec les hubs d’événements est **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, le **Nom d’hôte** est **iothub-ns-myiothub-1234.servicebus.windows.net** et l’**Espace de noms** est **iothub-ns-myiothub-1234**.
> 
> 

Vous pouvez ensuite utiliser n’importe quelle stratégie de sécurité d’accès partagé bénéficiant d’autorisations **ServiceConnect** pour vous connecter au hub d’événements ci-dessus.

Si vous devez générer une chaîne de connexion Event Hub en utilisant les informations ci-dessus, vous pouvez utiliser le modèle suivant :

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Voici une liste de Kits SDK et d’intégrations que vous pouvez utiliser avec les points de terminaison compatibles avec les hubs d’événements exposés par IoT Hub :

* [Client Event Hubs Java](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Vous pouvez afficher la [source spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) sur GitHub.
* [Intégration Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference"></a>Référence
### <a name="message-format"></a>Format de message
Les messages IoT Hub comprennent :

* Un ensemble de *propriétés système*. Propriétés interprétées ou définies par IoT Hub. Cet ensemble est prédéterminé.
* Un ensemble de *propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir et auquel elle peut accéder sans avoir à désérialiser le corps du message. IoT Hub ne modifie jamais ces propriétés.
* Un corps binaire opaque.

Pour plus d’informations sur la façon dont le message est encodé dans les différents protocoles, voir [API et kits de développement logiciel (SDK) IoT Hub][lnk-sdks].

Le tableau suivant répertorie l’ensemble des propriétés système dans les messages IoT Hub.

| Propriété | Description |
| --- | --- |
| MessageId |Identificateur correspondant au message défini par l’utilisateur, utilisé pour les modèles demande-réponse. Format : une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numéro de séquence |Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. |
| À |Une destination spécifiée dans les messages [cloud-à-appareil][lnk-c2d]. |
| ExpiryTimeUtc |Date et heure d’expiration du message. |
| EnqueuedTime |Date et heure de réception du message par IoT Hub. |
| CorrelationId |Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. |
| UserId |Un ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. |
| Ack |Un générateur de messages de commentaires. Cette propriété est utilisée dans les messages cloud vers appareil pour demander à IoT Hub de générer des messages de commentaires à la suite de la consommation du message par l’appareil. Valeurs possibles : **none** (par défaut) : aucun message de commentaires n’est généré ; **positive** : recevoir un message de commentaires si le message est achevé ; **negative** : recevoir un message de commentaires si le message a expiré (ou si le nombre maximal de remises a été atteint) sans être achevé par l’appareil, ou **full** : propriétés à la fois positive et négative. Pour plus d’informations, voir [Commentaires de messages][lnk-feedback]. |
| ConnectionDeviceId |Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété **deviceId** de l’appareil qui a envoyé le message. |
| ConnectionDeviceGenerationId |Un ID défini par IoT Hub sur les messages appareil vers cloud. Il contient la propriété **generationId** (conformément aux [Propriétés d’identité des appareils][lnk-device-properties]) de l’appareil qui a envoyé le message. |
| ConnectionAuthMethod |Une méthode d’authentification définie par IoT Hub sur les messages appareil vers cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. Pour plus d’informations, voir [Prévention d’usurpation d’identité entre un appareil et le cloud][lnk-antispoofing]. |

### <a name="communication-protocols"></a>Protocoles de communication
IoT Hub prend en charge les protocoles MQTT, [AMQP][lnk-amqp], AMQP sur WebSockets et HTTP/1 pour les communications côté appareil. Le tableau suivant fournit des recommandations de haut niveau pour votre choix de protocole :

| Protocole | Quand choisir ce protocole |
| --- | --- |
| MQTT |Utiliser sur tous les appareils qui ne nécessitent pas l’utilisation du protocole WebSocket. |
| AMQPS |Utiliser sur les passerelles de champ et de cloud pour tirer parti du multiplexage de connexion sur les appareils. <br/>  Utiliser lorsque vous devez vous connecter sur le port 443. |
| HTTPS |Utiliser pour les appareils qui ne peuvent pas prendre en charge les autres protocoles. |

Prenez en compte les points suivants lorsque vous choisissez votre protocole pour les communications côté appareil :

* **Modèle Cloud vers appareil**. HTTP/1 ne dispose pas d’un moyen efficace de mettre en œuvre la transmission des messages par le serveur. Par conséquent, lorsque vous utilisez HTTP/1, les appareils interrogent IoT Hub pour rechercher les messages cloud vers appareil. Cette approche est inefficace pour l’appareil et pour IoT Hub. Conformément aux recommandations actuelles concernant HTTP/1, chaque appareil doit interroger la présence de messages toutes les 25 minutes ou plus. En revanche, AMQP et MQTT prennent en charge les notifications Push sur le serveur lors de la réception de messages cloud vers appareil. Ils permettent d’obtenir des notifications Push immédiates pour les messages de l’IoT Hub vers l’appareil. Si la latence de livraison pose problème, AMQP ou MQTT sont les meilleurs protocoles à utiliser. Pour les appareils rarement connectés, HTTP/1 fonctionne aussi bien.
* **Passerelles de champ**. Lorsque vous utilisez HTTP/1 et MQTT, il est impossible de connecter plusieurs appareils (chacun avec ses propres informations d’identification par appareil) à l’aide de la même connexion TLS. Par conséquent, ces protocoles ne sont donc pas la solution optimale lors de l’implémentation de [scénarios de passerelle de champ][lnk-azure-gateway-guidance], car ils nécessitent une connexion TLS entre la passerelle de champ et IoT Hub pour chaque appareil connecté à celle-ci.
* **Appareils faibles en ressources**. Les bibliothèques MQTT et HTTP/1 sont moins encombrantes que les bibliothèques AMQP. Donc, si l’appareil dispose de ressources limitées (par exemple, moins de 1 Mo de mémoire RAM), ces protocoles sont peut-être les seuls protocoles d’implémentation disponibles.
* **Traversée réseau**. Le standard MQTT écoute sur le port 8883, ce qui peut entraîner des problèmes dans les réseaux fermés aux protocoles autres que HTTP. HTTP et AMQP (sur WebSockets) sont tous les deux disponibles pour utilisation dans ce scénario.
* **Taille de charge utile**. MQTT et AMQP sont des protocoles binaires qui génèrent des charges utiles plus compactes que HTTP/1.

> [!NOTE]
> Lorsque vous utilisez HTTP/1, chaque appareil doit envoyer des interrogations pour les messages cloud-à-appareil toutes les 25 minutes, voire plus. Toutefois, au cours du développement, il est clairement acceptable d’avoir des fréquences d’interrogation plus régulières que toutes les 25 minutes.
> 
> 

### <a name="port-numbers"></a>Numéros de ports
Les appareils peuvent communiquer avec IoT Hub dans Azure à l’aide de divers protocoles. En règle générale, le choix du protocole dépend des exigences spécifiques de la solution. Le tableau suivant répertorie les ports de sortie qui doivent être ouverts pour qu’un appareil puisse utiliser un protocole spécifique :

| Protocole | Port(s) |
| --- | --- |
| MQTT |8883 |
| AMQP |5671 |
| AMQP sur WebSockets |443 |
| HTTPS |443 |
| LWM2M (gestion des appareils) |5684 |

Lorsque vous avez créé un IoT Hub dans une région Azure, le hub conserve la même adresse IP pendant sa durée de vie. Toutefois, pour maintenir la qualité du service, si Microsoft le fait passer à une autre unité d’échelle, il reçoit une nouvelle adresse IP.

### <a name="notes-on-mqtt-support"></a>Remarques sur la prise en charge MQTT
IoT Hub implémente le protocole MQTT v3.1.1 avec le comportement spécifique et les limitations suivantes :

* **QoS 2 n’est pas pris en charge**. Quand un client d’appareil publie un message avec **QoS 2**, IoT Hub interrompt la connexion réseau. Quand un client d’appareil s’abonne à une rubrique avec **QoS 2**, IoT Hub accorde le niveau QoS 1 maximal dans le paquet **SUBACK**.
* **Les messages Retain ne sont pas persistants**. Si un client d’appareil publie un message avec l’indicateur RETAIN (conserver) défini sur 1, IoT Hub ajoute la propriété d’application **x-opt-retain** au message. Dans ce cas, IoT Hub ne conserve pas le message, mais le transmet à l’application principale.

Pour plus d’informations, voir [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt].

Enfin, il est important de consulter la section [Passerelle de protocole Azure IoT][lnk-azure-protocol-gateway], qui explique comment déployer une passerelle personnalisée hautes performances communiquant directement avec IoT Hub. La passerelle de protocole Azure IoT vous permet de personnaliser le protocole de l’appareil pour prendre en charge des déploiements MQTT de type « brownfield » ou autres protocoles personnalisés. Toutefois, cette approche nécessite un auto-hébergement et l’utilisation d’une passerelle de protocole personnalisée.

### <a name="additional-reference-material"></a>Matériel de référence supplémentaire
Autres rubriques de référence dans le Guide du développeur :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre lors de l’utilisation du service.
* La rubrique [Kits de développement logiciel (SDK) de services et appareils IoT Hub][lnk-sdks] répertorie les kits de développement logiciel en différents langages que vous pouvez utiliser lors du développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* La rubrique [Langage de requête pour les représentations, les méthodes et les travaux][lnk-query] décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur des représentations d’appareil, des méthodes et des travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez comment envoyer et recevoir des messages avec IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur :

* [Télécharger des fichiers à partir d’un appareil][lnk-devguide-upload]
* [Gérer les identités des appareils][lnk-devguide-identities]
* [Contrôler l’accès à IoT Hub][lnk-devguide-security]
* [Utiliser des représentations d’appareil pour synchroniser les données d’état et de configuration][lnk-devguide-device-twins]
* [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

* [Mise en route d’Azure IoT Hub][lnk-getstarted-tutorial]
* [Envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d-tutorial]
* [Traitement des messages appareil-à-cloud IoT Hub][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: https://www.amqp.org/
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md


<!--HONumber=Oct16_HO2-->


