<properties
 pageTitle="Rubriques du guide du développeur pour IoT Hub | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub portant sur les points de terminaison, la sécurité, le registre des identités des appareils et la messagerie pour IoT Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Guide du développeur Azure IoT Hub

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal.

Azure IoT Hub assure :

* La sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès
* Une messagerie évolutive appareil-à-cloud et cloud-à-appareil fiable
* Une connectivité des appareils aisée avec les bibliothèques d’appareils pour les langages et les plateformes les plus courants.

Cet article aborde les thèmes suivants :

- [Points de terminaison](#endpoints). Cette section décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
- [Registre des identités des appareils](#device-identity-registry). Cette section décrit les informations stockées par chaque registre des identités des appareils d’IoT Hub, ainsi que la manière d’y accéder et de les modifier.
- [Sécurité](#security). Cette section décrit le modèle de sécurité utilisé pour autoriser l’accès à la fonctionnalité IoT Hub pour les appareils et les composants du cloud.
- [Messagerie](#messaging). Cette section décrit les fonctionnalités de messagerie (appareil-à-cloud et cloud-à-appareil) qui sont exposées par IoT Hub.
- [Quotas et limitation](#throttling). Cette section résume les quotas qui s’appliquent à votre utilisation d’IoT Hub.

## Points de terminaison <a id="endpoints"></a>

Azure IoT Hub est un service mutualisé qui propose ses fonctionnalités à différents acteurs. Le schéma qui suit illustre les différents points de terminaison proposés par IoT Hub.

![Points de terminaison IoT Hub][img-endpoints]

Vous trouverez ci-dessous une description des points de terminaison :

* **Fournisseur de ressources** : le fournisseur de ressources IoT Hub expose une interface [Azure Resource Manager][lnk-arm] qui permet aux titulaires d’abonnements Azure de créer des hubs IoT, de les supprimer et de mettre à jour leurs propriétés. Les propriétés de IoT Hub régissent les stratégies de sécurité au niveau du hub, par opposition au contrôle d’accès au niveau de l’appareil (voir [Contrôle d’accès](#accesscontrol) ci-dessous), ainsi que les options fonctionnelles pour les messages cloud-à-appareil et appareil-à-cloud. Le fournisseur de ressources vous permet également d’[exporter les identités des appareils](#importexport).
* **Gestion d’identité de l’appareil** : chaque hub IoT expose un ensemble de points de terminaison HTTP REST afin de gérer les identités des appareils (par exemple, pour les opérations de création, de récupération, de mise à jour et de suppression). Les identités des appareils sont utilisées pour l’authentification des appareils et le contrôle d’accès. Consultez la rubrique [Registre des identités des appareils](#device-identity-registry) pour plus d’informations.
* **Points de terminaison des appareils** : pour chaque appareil approvisionné dans le registre des identités des appareils, IoT Hub expose un ensemble de points de terminaison qu’un appareil peut utiliser pour envoyer et recevoir des messages :
    - *Envoyer des messages appareil-à-cloud*. Utilisez ce point de terminaison pour envoyer des messages appareil-à-cloud. Pour plus d’informations, consultez [Messages appareil-à-cloud](#d2c)
    - *Recevoir des messages cloud-à-appareil*. Un appareil utilise ce point de terminaison pour recevoir les messages cloud-à-appareil qui lui sont adressés. Pour plus d’informations, consultez [Messages cloud-à-appareil](#c2d).

    Ces points de terminaison sont exposés à l’aide de protocoles HTTP 1.1, [MQTT v3.1.1][lnk-mqtt] et [AMQP 1.0][lnk-amqp]. Notez que le protocole AMQP est également disponible sur [WebSockets][lnk-websockets], sur le port 443.
* **Points de terminaison de service** : chaque IoT Hub expose un ensemble de points de terminaison que votre système principal d’application peut utiliser pour communiquer avec vos appareils. Ces points de terminaison sont actuellement exposés uniquement à l’aide du protocole [AMQP][lnk-amqp].
    - *Recevoir des messages appareil-à-cloud*. Ce point de terminaison est compatible avec [Azure Event Hubs][lnk-event-hubs] et peut être utilisé par un service principal pour lire tous les messages appareil-à-cloud envoyés par vos appareils. Pour plus d’informations, consultez [Messages appareil-à-cloud](#d2c)
    - *Envoyer des messages cloud-à-appareil et recevoir des accusés de remise*. Ces points de terminaison autorisent votre système principal d’application à envoyer des messages cloud-à-appareil et à recevoir les accusés de réception ou d’expiration correspondants. Pour plus d’informations, consultez [Messages cloud-à-appareil](#c2d).

L’article [API et Kits de développement logiciel (SDK) IoT Hub][lnk-apis-sdks] décrit les différentes méthodes permettant d’accéder à ces points de terminaison.

Enfin, il est important de noter que tous les points de terminaison IoT Hub utilisent le protocole [TLS][lnk-tls] et qu’aucun point de terminaison n’est jamais exposé sur des canaux non chiffrés/non sécurisés.

### Lecture à partir de points de terminaison compatibles Event Hubs. <a id="eventhubcompatible"></a>

Lorsque vous utilisez le [Kit Azure Service Bus SDK pour .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) ou [Event Hubs - Hôte du processeur d’événements][], vous pouvez utiliser n’importe quelle chaîne de connexion IoT Hub avec les autorisations appropriées, puis utiliser **messages/événements** comme nom de hub d’événements.

Lorsque vous utilisez des Kits de développement logiciel (SDK) ou intégrations de produits qui n’ont pas connaissance d’IoT Hub, vous devez récupérer un point de terminaison compatible avec les hubs d’événements et un nom de hub d’événements à partir des paramètres IoT Hub dans le [portail Azure][] :

1. Dans le panneau IoT Hub, cliquez sur **Paramètres**, puis sur **Messagerie**.
2. Dans la section **Paramètres appareil-à-cloud**, vous trouverez un **point de terminaison compatible avec les hubs d’événements**, un **nom compatible avec les hubs d’événements** et des valeurs de **Partitions**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] Si le Kit de développement logiciel (SDK) requiert une valeur **Nom d’hôte** ou **Espace de noms**, supprimez le modèle du **point de terminaison compatible Event Hub**. Par exemple, si votre point de terminaison compatible avec les hubs d’événements est ****sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, le **Nom d’hôte** est **iothub-ns-myiothub-1234.servicebus.windows.net** et l’**Espace de noms** est **iothub-ns-myiothub-1234**.

Vous pouvez ensuite utiliser n’importe quelle stratégie de sécurité d’accès partagé bénéficiant d’autorisations **ServiceConnect** pour vous connecter au hub d’événements ci-dessus.

Si vous devez générer une chaîne de connexion Event Hub en utilisant les informations ci-dessus, vous pouvez utiliser le modèle suivant :

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Voici une liste de Kits SDK et d’intégrations que vous pouvez utiliser avec les points de terminaison compatibles avec les hubs d’événements exposés par IoT Hub :

* [Client Event Hubs Java](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Vous pouvez afficher la [source spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) sur GitHub.
* [Intégration Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## Registre des identités des appareils

Chaque IoT Hub dispose d’un registre des identités des appareils servant à créer des ressources par appareil dans le service, par exemple, une file d’attente contenant les messages cloud-à-appareil envoyés en cours. Il autorise aussi l’accès aux points de terminaison des appareils, comme expliqué dans la section [Contrôle d’accès](#accesscontrol).

À un niveau supérieur, le registre des identités des appareils est une collection compatible REST de ressources d’identité des appareils. Les sections suivantes détaillent les propriétés des ressources d’identité des appareils et les opérations que le registre autorise sur les identités.

> [AZURE.NOTE] Consultez l’article [API et Kits de développement logiciel (SDK) IoT Hub][lnk-apis-sdks] pour plus d’informations sur le protocole HTTP et les Kits de développement logiciel (SDK) que vous pouvez utiliser pour interagir avec le registre des identités des appareils.

### Propriétés d’identité des appareils <a id="deviceproperties"></a>

Les identités des appareils sont représentées sous forme de documents JSON avec les propriétés suivantes.

| Propriété | Options | Description |
| -------- | ------- | ----------- |
| deviceId | obligatoire, en lecture seule sur les mises à jour | Une chaîne respectant la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | obligatoire, en lecture seule | Une chaîne qui respecte la casse, générée par le hub et allant jusqu’à 128 caractères. Elle permet de distinguer les appareils dotés du même **deviceId** lorsqu’ils ont été supprimés et recréés. |
| etag | obligatoire, en lecture seule | Une chaîne représentant un etag faible pour l’identité d’appareil, conformément à [RFC7232][lnk-rfc7232].|
| auth | facultatif | Un objet composite contenant des informations d’authentification et des éléments de sécurité. |
| auth.symkey | facultatif | Un objet composite contenant une clé primaire et une clé secondaire, stockées au format base64. |
| status | required | Peut être **Activé** ou **Désactivé**. Si la propriété est définie sur **Activé**, l’appareil est autorisé à se connecter. Si la propriété est définie sur **Désactivé**, cet appareil ne peut pas accéder à un point de terminaison de l’appareil. |
| statusReason | facultatif | Chaîne de 128 caractères stockant le motif de l’état de l’identité de l’appareil. Tous les caractères UTF-8 sont autorisés. |
| statusUpdateTime | en lecture seule | Date et heure de la dernière mise à jour de l’état. |
| connectionState | en lecture seule | **Connecté** ou **Déconnecté** représente la vue IoT Hub de l’état de connexion de l’appareil. **Important **: ce champ doit être utilisé uniquement à des fins de développement et de débogage. L’état de la connexion est mis à jour uniquement pour les appareils utilisant les protocoles AMQP ou MQTT. Cet état est basé sur les pings au niveau du protocole (tests ping MQTT ou AMQP) et peut avoir un délai de 5 minutes au maximum. Pour ces raisons, de faux positifs peuvent survenir. Par exemple : un appareil peut être signalé comme étant connecté, alors qu’en réalité, celui-ci est déconnecté. |
| connectionStateUpdatedTime | en lecture seule | Date de la dernière mise à jour de l’état de la connexion. |
| lastActivityTime | en lecture seule | Date de la dernière connexion de l’appareil, de la dernière réception d’un message ou du dernier envoi d’un message. |

> [AZURE.NOTE] L’état de la connexion peut uniquement représenter la vue IoT Hub de l’état de la connexion. Les mises à jour à cet état peuvent être différées en fonction des conditions et des configurations du réseau.

### Opérations d’identité des appareils

Le registre des identités des appareils IoT Hub expose les opérations suivantes :

* Création d’une identité d’appareil
* Mise à jour d’une identité d’appareil
* Récupération d’une identité d’appareil par ID
* Suppression d’une identité d’appareil
* Création de listes contenant jusqu’à 1 000 identités
* Exportation de toutes les identités vers le Blob Storage
* Importation de toutes les identités depuis le Blob Storage

Toutes ces opérations autorisent l’utilisation de l’accès concurrentiel optimiste comme spécifié dans [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] La seule façon de récupérer toutes les identités dans le registre des identités d’un hub consiste à utiliser la fonctionnalité [Exporter](#importexport).

Un registre des identités des appareils IoT Hub :

- ne contient pas de métadonnées de l’application ;
- est accessible en tant que dictionnaire à l’aide de la clé **deviceId** ;
- ne prend pas en charge les requêtes expressives.

Une solution IoT possède généralement une zone de stockage distincte spécifique à la solution qui contient les métadonnées propres à l’application. Dans une solution de développement intelligente, par exemple, la zone de stockage spécifique à la solution doit enregistrer l’espace dans lequel un capteur de température sera déployé.

> [AZURE.IMPORTANT] Vous devez uniquement utiliser le registre des identités des appareils pour les opérations de gestion des périphériques et d’approvisionnement. Les opérations à haut débit ne doivent pas dépendre de l’exécution d’opérations dans le registre des identités des appareils au moment de leur exécution. Par exemple, la vérification de l’état de la connexion d’un appareil avant l’envoi d’une commande n’est pas un modèle pris en charge. Veillez à vérifier les [taux de limitation](#throttling) pour le registre des identités des appareils et le modèle de [pulsation de l’appareil][lnk-guidance-heartbeat].

### Désactivation des appareils

Vous pouvez désactiver les appareils en mettant à jour la propriété **status** d’une identité dans le registre. Généralement, cette option est utilisée dans deux scénarios :

- Au cours d’un processus d’orchestration d’approvisionnement. Pour en savoir plus, voir [Conception de votre solution - Approvisionnement des appareils][lnk-guidance-provisioning].
- Si, pour une raison quelconque, vous pensez qu’un appareil est compromis ou non autorisé.

### Importer et exporter les identités des appareils <a id="importexport"></a>

Vous pouvez exporter des identités d’appareils en bloc à partir du registre des identités d’un IoT Hub, par le biais d’opérations asynchrones sur le [point de terminaison d’un fournisseur de ressources IoT Hub ](#endpoints). Les exportations sont des tâches à long terme qui utilisent un conteneur d’objets blob fourni par le client pour enregistrer les données relatives à l’identité des appareils lues dans le registre des identités :

- Pour plus d’informations sur l’importation et l’exportation des API, consultez [Azure IoT Hub - API de fournisseur de ressources][lnk-resource-provider-apis].
- Pour en savoir plus sur l’exécution de tâches d’importation et d’exportation, voir [Gestion en bloc des identités d’appareils IoT Hub][lnk-bulk-identity].

Vous pouvez importer des identités d’appareils en bloc dans le registre des identités d’un IoT Hub, par le biais d’opérations asynchrones sur le [point de terminaison d’un fournisseur de ressources IoT Hub ](#endpoints). Les importations sont des tâches à long terme qui utilisent des données dans un conteneur d’objets blob, fourni par le client, pour écrire les données relatives à l’identité des appareils dans le registre des identités.

- Pour plus d’informations sur l’importation et l’exportation des API, consultez [Azure IoT Hub - API de fournisseur de ressources][lnk-resource-provider-apis].
- Pour en savoir plus sur l’exécution de tâches d’importation et d’exportation, voir [Gestion en bloc des identités d’appareils IoT Hub][lnk-bulk-identity].

## Sécurité <a id="security"></a>

Cette section décrit les options de sécurisation d’Azure IoT Hub.

### Contrôle d’accès <a id="accesscontrol"></a>

Azure IoT Hub utilise l’ensemble d’*autorisations* qui suit pour accorder l’accès à chaque point de terminaison de chaque IoT Hub. Les autorisations limitent l’accès à un hub IoT selon la fonctionnalité.

* **RegistryRead**. Accorde l’accès en lecture au registre des identités des appareils. Pour plus d’informations, consultez la rubrique [Registre d’identité des appareils](#device-identity-registry).
* **RegistryReadWrite**. Accorde l’accès en lecture et en écriture au registre des identités des appareils. Pour plus d’informations, consultez la rubrique [Registre d’identité des appareils](#device-identity-registry).
* **ServiceConnect**. Accorde l’accès à la communication de services cloud et à la surveillance des points de terminaison. Par exemple, elle permet aux services cloud principaux de recevoir des messages appareil-à-cloud, d’envoyer des messages cloud-à-appareil et de récupérer les accusés de remise correspondants.
* **DeviceConnect**. Accorde l’accès aux points de terminaison de communication d’appareils. Par exemple, elle permet d’envoyer des messages appareil-à-cloud et de recevoir des messages cloud-à-appareil. Cette autorisation est utilisée par les appareils.

Vous pouvez accorder les autorisations de différentes manières :

* **Stratégies d’accès partagé au niveau du concentrateur**. Les stratégies d’accès partagé peuvent accorder n’importe quelle combinaison des autorisations mentionnées dans la section qui précède. Vous pouvez définir des stratégies dans le [portail Azure][lnk-management-portal] ou par programmation à l’aide des [API du fournisseur de ressources Azure IoT Hub][lnk-resource-provider-apis]. Un hub IoT qui vient d’être créé a les stratégies par défaut suivantes :

    - *iothubowner* : stratégie jouissant de toutes les autorisations
    - *service* : stratégie jouissant de l’autorisation **ServiceConnect**
    - *device* : stratégie jouissant de l’autorisation **DeviceConnect**
    - *registryRead* : stratégie jouissant de l’autorisation **RegistryRead**
    - *registryReadWrite* : stratégie jouissant des autorisations **RegistryRead** et **RegistryWrite**

* **Informations d’identification de sécurité par appareil**. Chaque IoT Hub contient un [registre d’identité des appareils](#device-identity-registry). Pour chaque appareil figurant dans ce registre, vous pouvez configurer des informations d’identification de sécurité qui accordent des autorisations **DeviceConnect** incluses dans l’étendue des points de terminaison des appareils correspondants.

**Exemple**. Dans une solution IoT classique :
- Le composant de gestion des appareils utilise la stratégie *registryReadWrite*.
- Le composant de processeur d’événements utilise la stratégie *service*.
- Le composant de logique métier des appareils d’exécution utilise la stratégie *service*.
- Les appareils individuels se connectent à l’aide d’informations d’identification stockées dans le registre des identités du hub IoT.

Pour obtenir des conseils sur les aspects liés à la sécurité d’IoT Hub, voir la section Sécurité dans [Conception de votre solution][lnk-guidance-security].

### Authentification

Azure IoT Hub accorde l’accès aux points de terminaison en vérifiant un jeton par rapport aux stratégies d’accès partagé et aux informations d’identification de sécurité du registre des identités des appareils.

Les informations d’identification de sécurité telles que les clés symétriques ne sont jamais envoyées sur le réseau.

> [AZURE.NOTE] Le fournisseur de ressources Azure IoT Hub est sécurisé au moyen de votre abonnement Azure, à l’instar de tous les fournisseurs dans [Azure Resource Manager][lnk-azure-resource-manager].

Reportez-vous à l’article sur les [jetons de sécurité IoT Hub][lnk-sas-tokens] pour plus d’informations sur la construction et l’utilisation des jetons de sécurité.

#### Spécifications de protocole

Chaque protocole pris en charge (par exemple, AMQP, MQTT et HTTP) transporte des jetons de différentes façons.

Le protocole HTTP implémente l’authentification en incluant un jeton valide dans l’en-tête de demande **Authorization**. Un paramètre de demande appelé **Authorization** peut également transporter le jeton.

Lorsque vous utilisez [AMQP][lnk-amqp], IoT Hub prend en charge [SASL PLAIN][lnk-sasl-plain] et la [sécurité basée sur des revendications AMQP][lnk-cbs].

Si la sécurité est basée sur des revendications AMQP, la norme indique comment transmettre les jetons répertoriés ci-dessus.

Pour SASL PLAIN, le **nom d’utilisateur** peut être :

* `{policyName}@sas.root.{iothubName}` s’il s’agit de jetons de niveau concentrateur.
* `{deviceId}` s’il s’agit de jetons inclus dans l’étendue d’un appareil.

Dans les deux cas, le champ de mot de passe contient le jeton, comme décrit dans l’article sur les [jetons de sécurité IoT Hub][lnk-sas-tokens].

Lorsque vous utilisez MQTT, le paquet CONNECT utilise deviceid en tant que ClientId, {iothubhostname} / {deviceId} dans le champ Nom d’utilisateur et un jeton SAP dans le champ Mot de passe. {iothubhostname} doit être le nom canonique (CNAME) complet d’IoT Hub (par exemple, contoso.azure-devices.net).

##### Exemple : #####

Nom d’utilisateur (DeviceId respecte la casse) : `iothubname.azure-devices.net/DeviceId`

Mot de passe (générer SAP avec l’Explorateur d’appareils) : `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Les [kits de développement logiciel (SDK) Azure IoT Hub][lnk-apis-sdks] génèrent automatiquement des jetons lors de la connexion au service. Dans certains cas, les kits de développement logiciel ne prennent pas en charge l’ensemble des protocoles ou méthodes d’authentification.

#### Comparaison entre SASL PLAIN et CBS

Lorsque vous utilisez SASL PLAIN, un client qui se connecte à un IoT Hub peut utiliser un jeton unique pour chaque connexion TCP. Lorsque le jeton expire, la connexion TCP est déconnectée du service, ce qui déclenche une reconnexion. Bien que non problématique pour un composant de serveur d’applications principal, ce comportement peut créer de graves dommages pour une application côté appareils pour les motifs suivants :

*  Les passerelles se connectent généralement au nom de nombreux appareils. Lorsque vous utilisez SASL PLAIN, elles doivent créer une connexion TCP distincte pour chaque appareil se connectant à un IoT Hub. Cela augmente considérablement la consommation des ressources d’alimentation et de mise en réseau, ainsi que la latence de chaque connexion d’appareil.
* Les appareils avec des contraintes de ressources seront affectés par l’utilisation accrue des ressources pour se reconnecter après chaque expiration du jeton.

### Étendue des informations d’identification au niveau du hub

Vous pouvez étendre les stratégies de sécurité au niveau du hub en créant des jetons avec un URI de ressource restreint. Par exemple, le point de terminaison pour l’envoi de messages vers le cloud à partir d’un appareil est **/devices/{deviceId}/messages/events**. Vous pouvez également utiliser une stratégie d’accès partagé au niveau du hub avec les autorisations **DeviceConnect** pour signer un jeton dont l’URI de ressource est **/devices/{deviceId}**, créant ainsi un jeton uniquement utilisable pour envoyer des messages à des appareils pour le compte d’un appareil **deviceId**.

Il s’agit d’un mécanisme semblable à la [stratégie de publication d’Event Hubs][lnk-event-hubs-publisher-policy] qui vous permet d’implémenter des méthodes d’authentification personnalisées, comme expliqué dans la section Sécurité de l’article [Conception de votre solution][lnk-guidance-security].

## Messagerie

IoT Hub offre des primitives de message pour communiquer :

- [Cloud-à-appareil](#c2d) : à partir d’un serveur principal d’applications (*service* ou *cloud*).
- [Appareil-à-cloud](#d2c) : à partir d’un appareil vers un serveur principal d’applications.

Les principales propriétés de la fonctionnalité de messagerie IoT Hub sont la fiabilité et la durabilité des messages. Elle active la résilience de la connectivité intermittente côté appareils et des pics de chargement dans le traitement d’événements côté cloud. IoT Hub implémente *au moins une fois* des garanties de remise pour l’envoi de messages appareil-à-cloud et cloud-à-appareil.

IoT Hub prend en charge plusieurs protocoles d’appareil (par exemple, MQTT, AMQP et HTTP). Pour prendre en charge une interopérabilité transparente entre les différents protocoles, IoT Hub définit un format de message commun qui est pris en charge par tous les protocoles d’appareil.

### Format de message <a id="messageformat"></a>

Les messages IoT Hub comprennent :

* Un ensemble de *propriétés système*. Il s’agit des propriétés interprétées ou définies par IoT Hub. Cet ensemble est prédéterminé.
* Un ensemble de *propriétés de l’application*. Il s’agit d’un dictionnaire de propriétés de chaîne que l’application peut définir et auquel elle peut accéder sans avoir à désérialiser le corps du message. IoT Hub ne modifie jamais ces propriétés.
* Un corps binaire opaque.

Consultez [API et kits de développement logiciel (SDK) IoT Hub][lnk-apis-sdks] pour plus d’informations sur la façon dont le message est encodé dans les différents protocoles.

Il s’agit de l’ensemble des propriétés système dans les messages IoT Hub.

| Propriété | Description |
| -------- | ----------- |
| MessageId | Identificateur correspondant au message défini par l’utilisateur, généralement utilisé pour les modèles demande-réponse. Format : une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numéro de séquence | Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. |
| À | Propriété utilisée dans les messages [cloud-à-appareil](#c2d) pour spécifier la destination. |
| ExpiryTimeUtc | Date et heure d’expiration du message. |
| EnqueuedTime | Date et heure de réception du message par IoT Hub. |
| CorrelationId | Propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. |
| UserId | Permet de spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. |
| Ack | Propriété utilisée dans les messages cloud-à-appareil pour demander à IoT Hub de générer des messages de commentaires à la suite de la consommation du message par l’appareil. Valeurs possibles : **none** (par défaut) : aucun message de commentaires n’est généré ; **positive** : recevoir un message de commentaires si le message est achevé ; **negative** : recevoir un message de commentaires si le message a expiré (ou si le nombre de remises maximal a été atteint) sans être achevé par l’appareil, **full** : propriétés à la fois positive et negative. Pour plus d’informations, consultez [Retours de messages](#feedback). |
| ConnectionDeviceId | Propriété définie par IoT Hub sur les messages appareil-à-cloud. Elle contient la propriété **deviceId** de l’appareil qui a envoyé le message. |
| ConnectionDeviceGenerationId | Propriété définie par IoT Hub sur les messages appareil-à-cloud. Elle contient la propriété **generationId** (conformément aux [Propriétés d’identité des appareils](#deviceproperties)) de l’appareil qui a envoyé le message. |
| ConnectionAuthMethod | Propriété définie par IoT Hub sur les messages appareil-à-cloud. Informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. Pour plus d’informations, consultez [Prévention d’usurpation d’identité entre un appareil et le cloud](#antispoofing).|

### Choix de votre protocole de communication <a id="amqpvshttp"></a>

IoT Hub prend en charge les protocoles [AMQP][lnk-amqp], AMQP sur WebSockets, MQTT et HTTP/1 pour les communications côté appareil. Voici une liste des considérations relatives à leur utilisation.

* **Modèle Cloud vers appareil**. HTTP/1 ne dispose pas d’un moyen efficace de mettre en œuvre la transmission des messages par le serveur. Par conséquent, lorsque vous utilisez HTTP/1, les appareils interrogent IoT Hub pour rechercher les messages cloud-à-appareil. Cela se révèle particulièrement inefficace pour l’appareil et pour IoT Hub. Selon les directives actuelles relatives à l’utilisation de HTTP/1, les appareils doivent interroger IoT Hub toutes les 25 minutes, voire plus. Par ailleurs, AMQP et MQTT prennent en charge la transmission des messages par le serveur à la réception de messages cloud-à-appareil et activent immédiatement la transmission des messages d’IoT Hub vers l’appareil. Si la latence de livraison pose problème, AMQP ou MQTT sont les meilleurs protocoles à utiliser. Pour les appareils quasiment jamais connectés, le protocole HTTP/1 fonctionne également.
* **Passerelles de champ**. Lorsque vous utilisez HTTP/1 et MQTT, il est impossible de connecter plusieurs appareils (chacun avec ses propres informations d’identification par appareil) à l’aide de la même connexion TLS. Ces protocoles ne sont donc pas la solution optimale lors de l’implémentation de [scénarios de passerelle de champ][lnk-azure-gateway-guidance], car ils nécessitent une connexion TLS entre la passerelle de champ et IoT Hub pour chaque appareil connecté à la passerelle de champ.
* **Appareils faibles en ressources**. Les bibliothèques MQTT et HTTP/1 sont moins encombrantes que les bibliothèques AMQP. Donc, si l’appareil dispose de peu de ressources (par exemple, de moins d’1 Mo de mémoire RAM), ces protocoles sont peut-être les seuls protocoles d’implémentation disponibles.
* **Traversée réseau**. Le standard MQTT écoute sur le port 8883. Cela peut entraîner des problèmes dans les réseaux fermés aux protocoles autres que HTTP. HTTP et AMQP (sur WebSockets) sont tous les deux disponibles pour utilisation dans ce scénario.
* **Taille de charge utile**. MQTT et AMQP sont des protocoles binaires qui sont beaucoup plus compacts que HTTP/1.

À un niveau élevé, vous devez utiliser AMQP (ou AMQP sur WebSocket) autant que possible, et utiliser MQTT uniquement lorsque les contraintes de ressources empêchent l’utilisation d’AMQP. HTTP/1 doit être utilisé uniquement si la traversée réseau et la configuration de réseau empêchent l’utilisation de MQTT et AMQP. En outre, lorsque vous utilisez HTTP/1, chaque appareil doit envoyer des interrogations pour les messages cloud-à-appareil toutes les 25 minutes, voire plus.

> [AZURE.NOTE] Au cours du développement, il est clairement acceptable d’avoir des fréquences d’interrogation plus régulières que toutes les 25 minutes.

<a id="mqtt-support">
#### Remarques sur la prise en charge MQTT
IoT Hub implémente le protocole MQTT v3.1.1 avec le comportement spécifique et les limitations suivantes :

  * **QoS 2 n’est pas pris en charge** : quand un client d’appareil publie un message avec **QoS 2**, IoT Hub interrompt la connexion réseau. Quand un client d’appareil s’abonne à une rubrique avec **QoS 2**, IoT Hub accorde le niveau QoS 1 maximal dans le paquet **SUBACK**.
  * **Retain** : si un client d’appareil publie un message avec l’indicateur RETAIN (conserver) défini sur 1, IoT Hub ajoute la propriété d’application **x-opt-retain** au message. Cela signifie qu’IoT Hub ne conserve pas le message, mais le transmet à l’application principale.
  
Consultez l’article [Prise en charge de MQTT au niveau d’IoT Hub][lnk-mqtt-support] pour plus de détails.

Enfin, il est important de consulter la section [Passerelle de protocole Azure IoT][lnk-azure-protocol-gateway], qui explique comment déployer une passerelle personnalisée hautes performances communiquant directement avec IoT Hub. La passerelle de protocole Azure IoT vous permet de personnaliser le protocole de l’appareil pour prendre en charge des déploiements MQTT de type « brownfield » ou autres protocoles personnalisés. Le principal inconvénient de cette approche est la nécessité d’héberger et de gérer soi-même une passerelle de protocole personnalisé.

### Appareil vers cloud <a id="d2c"></a>

Comme expliqué dans la section [Points de terminaison](#endpoints), les messages appareil-à-cloud sont envoyés via un point de terminaison accessible à l’appareil (**/devices/{deviceId}/messages/events**) et reçus via un point de terminaison accessible au service (**/messages/events**) compatible avec [Event Hubs][lnk-event-hubs]. Par conséquent, vous pouvez utiliser l’intégration et les Kits SDK standard Event Hubs pour recevoir des messages appareil-à-cloud.

IoT Hub implémente la messagerie Appareil vers cloud de façon très semblable à [Event Hubs][lnk-event-hubs], avec des messages Appareil vers cloud d’IoT Hub ressemblant davantage aux *événements* Event Hubs qu’aux *messages* [Service Bus][lnk-servicebus].

Les conséquences sont les suivantes :

* À l’instar des *événements* Event Hubs, les messages Appareil vers cloud sont durables et ils sont conservés dans un IoT Hub jusqu’à 7 jours (consultez [Options de configuration Appareil vers cloud](#d2cconfiguration)).
* Les messages appareil-à-cloud sont partitionnés en un ensemble fixe de partitions, qui est défini au moment de la création (consultez [Options de configuration appareil-à-cloud](#d2cconfiguration)).
* Comme pour Event Hubs, les clients lisant des messages appareil-à-cloud doivent gérer des partitions et des points de contrôle. Voir [Event Hubs - Événements de consommation][lnk-event-hubs-consuming-events].
* De même que pour les événements Event Hubs, les messages appareil-à-cloud ne doivent pas dépasser 256 Ko et peuvent être mis en lots pour optimiser les envois. Les lots doivent représenter 256 Ko de volume et 500 messages maximum.

Toutefois, il existe quelques différences importantes entre les messages appareil-à-cloud IoT Hub et Event Hubs :

* Comme expliqué dans la section [Sécurité](#security), IoT Hub permet l’authentification et le contrôle d’accès par appareil.
* IoT Hub autorise des millions d’appareils connectés simultanément (voir [Quotas et limitation](#throttling)), tandis qu’Event Hubs est limité à 5 000 connexions AMQP par espace de noms.
* IoT Hub n’autorise pas le partitionnement arbitraire à l’aide d’une clé de partition (**PartitionKey**). Les messages appareil-à-cloud sont partitionnés en fonction de leur **deviceId** d’origine.
* La mise à l’échelle IoT Hub est légèrement différente de celle d’Event Hubs. Pour plus d’informations, consultez la [Mise à l’échelle d’IoT Hub][lnk-guidance-scale].

Notez que cela ne signifie pas que vous pouvez remplacer Event Hubs par IoT Hub dans tous les scénarios. Par exemple, dans certains calculs de traitement d’événements, il peut être nécessaire de repartitionner les événements par rapport à une autre propriété ou à un autre champ avant d’analyser les flux de données. Dans ce cas, vous pouvez utiliser un hub d’événements pour dissocier les deux parties du pipeline de traitement de flux. Pour plus d’informations, consultez *Partitions* dans [Vue d’ensemble d’Azure Event Hubs][lnk-eventhub-partitions].

Pour plus d’informations sur la façon d’utiliser la messagerie Appareil vers cloud, reportez-vous à [API et kits de développement logiciel (SDK) IoT Hub][lnk-apis-sdks].

> [AZURE.NOTE] Si vous utilisez HTTP pour envoyer des messages appareil-à-cloud, les valeurs et les noms de propriétés ne peuvent contenir que des caractères alphanumériques ASCII plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Trafic autre que la télémétrie

Dans de nombreux cas, outre les points de données de télémétrie, les appareils envoient également des messages et demandes qui nécessitent une exécution et une gestion au niveau de la couche de logique métier d’application. Il s’agit, par exemple, des alertes critiques qui doivent déclencher une action spécifique au niveau du serveur principal, ou encore des réponses de l’appareil aux commandes envoyées par le serveur principal.

Pour plus d’informations sur la meilleure façon de traiter ce type de message, consultez [Traitement appareil-à-cloud][lnk-guidance-d2c-processing].

#### Options de configuration Appareil vers cloud <a id="d2cconfiguration"></a>

Un hub IoT expose les propriétés suivantes pour vous permettre de contrôler les messages appareil-à-cloud.

* **Nombre de partitions**. Configurez cette propriété lors de la création pour définir le nombre de partitions pour la réception d’événements appareil-à-cloud.
* **Durée de rétention**. Cette propriété spécifie la durée de rétention des messages appareil-à-cloud. La durée par défaut est de un jour, mais elle peut être augmentée à sept jours.

À l’instar d’Event Hubs, IoT Hub vous permet aussi de gérer des groupes de consommateurs sur le point de terminaison de la réception de messages appareil-à-cloud.

Toutes les propriétés ci-dessus peuvent être modifiées à la fois via le [portail Azure][lnk-management-portal] ou par programme via [Azure IoT Hub - API de fournisseur de ressources][lnk-resource-provider-apis].

#### Propriétés de la fonction anti-usurpation <a id="antispoofing"></a>

Pour éviter l’usurpation d’appareil dans les messages appareil-à-cloud, IoT Hub marque tous les messages avec les propriétés suivantes :

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Les deux premières propriétés contiennent le **deviceId** et le **generationId** de l’appareil d’origine, conformément aux [Propriétés d’identité des appareils](#deviceproperties).

La propriété **ConnectionAuthMethod** contient un objet sérialisé JSON avec les propriétés suivantes :

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### Cloud vers appareil <a id="c2d"></a>

Comme expliqué dans la section [Points de terminaison](#endpoints), vous pouvez envoyer des messages cloud-à-appareil via un point de terminaison accessible au service (**/messages/devicebound**), et un appareil peut recevoir ces messages via un point de terminaison spécifique de l’appareil (**/devices/{deviceId}/messages/devicebound**).

Chaque message cloud-à-appareil cible un seul appareil en définissant la propriété **to** sur **/devices/{deviceId}/messages/devicebound**.

**Important** : chaque file d’attente d’appareil peut contenir jusqu’à 50 messages Cloud vers appareil. Les tentatives d’envoi d’un nombre supérieur de messages au même appareil entraînent une erreur.

> [AZURE.NOTE] Quand vous envoyez des messages appareil-à-cloud, les valeurs et les noms de propriétés ne peuvent contenir que des caractères alphanumériques ASCII plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Cycle de vie des messages <a id="message lifecycle"></a>

Pour mettre en œuvre *au moins une fois* la garantie de remise, les messages Cloud vers appareil sont conservés dans les files d’attente par appareil et les appareils doivent explicitement reconnaître leur *achèvement* pour qu’IoT Hub puisse les supprimer de la file d’attente. Cela garantit la résilience contre les échecs de connectivité et d’appareils.

L’illustration suivante présente le graphique d’état du cycle de vie d’un message cloud-à-appareil.

![Cycle de vie des messages cloud-à-appareil][img-lifecycle]

Quand le service envoie un message, celui-ci est considéré comme *en file d’attente*. Quand un appareil doit *recevoir* un message, IoT Hub *verrouille* le message (défini à l’état **Invisible**) pour autoriser d’autres threads sur le même appareil à commencer à recevoir d’autres messages. Quand un thread d’appareil a fini de traiter un message, il notifie IoT Hub en *terminant* le message.

Un appareil peut également :

- *rejeter* le message, ce qui amène IoT Hub à lui attribuer l’état **Deadlettered** (lettre morte) ;
- *abandonner* le message, ce qui amène IoT Hub à replacer le message dans la file d’attente avec l’état **Enqueued** (En file attente).

Il est possible qu’un thread ne parvienne pas à traiter un message sans en avertir IoT Hub. Dans ce cas, les messages passent automatiquement de l’état **Invisible** à l’état **Enqueued** (En file d’attente) après un *délai de visibilité (ou de verrouillage)* dont la valeur par défaut est d’une minute. Un message peut passer de l’état **Enqueued** (En file d’attente) à l’état **Invisible** et inversement, au maximum le nombre de fois spécifié dans la propriété *Nombre maximal de diffusions* sur IoT Hub. Une fois ce nombre de transitions atteint, IoT Hub attribue au message l’état **Deadlettered** (Lettre morte). De même, IoT Hub attribue à un message l’état **Deadlettered** (Lettre morte) à l’issue de son délai d’expiration (consultez [Durée de vie](#ttl)).

Pour un didacticiel relatif aux messages Cloud vers Appareil, consultez [Prise en main des messages Cloud vers Appareil Azure IoT Hub][lnk-getstarted-c2d-tutorial]. Pour consulter les rubriques de référence à propos des différences de présentation de la fonctionnalité Cloud vers appareil entre les API et le kit de développement logiciel, consultez [Kits de développement logiciel (SDK) et API d’IoT Hub][lnk-apis-sdks].

> [AZURE.NOTE] Généralement, les messages cloud-à-appareil sont achevés à chaque fois que la perte du message n’affecte pas la logique d’application. Cela peut se produire dans de nombreux scénarios différents. Par exemple : le contenu du message a été conservé sur l’espace de stockage local, une opération a été exécutée avec succès ou le message transporte des informations temporaires dont la perte n’aurait aucune répercussion sur le fonctionnement de l’application. Parfois, pour les tâches à long terme, vous pouvez envoyer le message cloud-à-appareil après avoir maintenu la description de la tâche sur le stockage local, puis notifier le système principal d’application en envoyant un ou plusieurs messages appareil-à-cloud, à différentes étapes de progression de la tâche.

#### Durée de vie <a id="ttl"></a>

Chaque message cloud-à-appareil est doté d’un délai d’expiration. Il peut être défini explicitement par le service (propriété **ExpiryTimeUtc**) ou il est défini par IoT Hub avec la *durée de vie* par défaut spécifiée en tant que propriété IoT Hub. Consultez [Options de configuration Cloud vers appareil](#c2dconfiguration).

> [AZURE.NOTE] Un moyen courant de tirer parti de l’expiration du message consiste à définir des valeurs de durée de vie courtes pour éviter l’envoi de messages à des appareils déconnectés. Vous obtenez le même résultat qu’avec la gestion de l’état de connexion de l’appareil, tout en étant beaucoup plus efficace. Il est également possible, en demandant des accusés de réception des messages, d’être informé par IoT Hub des appareils qui peuvent recevoir des messages et de ceux qui ne sont pas en ligne ou qui sont en état d’échec.

#### Commentaires de messages <a id="feedback"></a>

Lorsque vous envoyez un message cloud-à-appareil, le service peut demander la remise d’un commentaire de message concernant l’état final de ce message.

- Si vous définissez la propriété **Ack** sur **positive**, IoT Hub génère un message de commentaire si et seulement si le message cloud-à-appareil est à l’état **Completed** (Terminé).
- Si vous définissez la propriété **Ack** sur **negative**, IoT Hub génère un message de commentaire si et seulement si le message cloud-à-appareil est à l’état **Deadlettered** (Lettre morte).
- Si la propriété **Ack** est définie sur **full**, IoT Hub génère un message de commentaire dans les deux cas.

> [AZURE.NOTE] Si la propriété **Ack** est définie sur **full** et si aucun message de commentaire n’est reçu, cela signifie que le message de commentaire a expiré et que le service ne peut pas savoir ce qui est arrivé au message d’origine. Dans la pratique, un service doit s'assurer qu'il peut traiter les commentaires avant leur expiration. Le délai d'expiration maximal étant de deux jours, vous devez par conséquent veiller à disposer de suffisamment de temps pour rendre le service opérationnel en cas de défaillance.

Comme expliqué dans la section [Points de terminaison](#endpoints), IoT Hub fournit des commentaires sous la forme de messages via un point de terminaison accessible au service (**/messages/servicebound/feedback**). La sémantique de réception des commentaires est identique à celle des messages cloud-à-appareil ayant le même [cycle de vie des messages](#message lifecycle). Chaque fois que c’est possible, des commentaires de messages sont mis en lot dans un seul message, au format suivant.

Chaque message récupéré par un appareil à partir du point de terminaison de commentaires a les propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTime | Horodatage indiquant la date et l’heure de création du message. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Le corps est un tableau sérialisé JSON des enregistrements, chacun disposant des propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | Horodatage indiquant la date et l’heure du résultat du message. Par exemple, l’achèvement de l’appareil ou l’expiration du message. |
| OriginalMessageId | **MessageId** du message Cloud vers appareil auquel appartiennent ces informations de commentaires. |
| StatusCode | Entier obligatoire. Utilisé dans les messages de commentaires générés par IoT Hub. <br/> 0 = succès <br/> 1 = message expiré <br/> 2 = nombre maximal de remises dépassé <br/> 3 = message rejeté |
| Description | Valeurs de chaîne pour **StatusCode**. |
| DeviceId | **DeviceId** de l’appareil cible du message Cloud vers appareil auquel appartient ce commentaire. |
| DeviceGenerationId | **DeviceGenerationId** de l’appareil cible du message Cloud vers appareil auquel appartient ce commentaire. |


**Important**. Le service doit spécifier un **MessageId** pour le message Cloud vers appareil afin de pouvoir mettre en corrélation ses commentaires avec le message d’origine.

**Exemple**. Voici un exemple de corps de message de commentaires.

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

#### Options de configuration Cloud vers appareil <a id="c2dconfiguration"></a>

Chaque IoT Hub expose les options de configuration suivantes pour les messages cloud-à-appareil :

| Propriété | Description | Plage et valeur par défaut |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Durée de vie par défaut pour les messages cloud-à-appareil. | Intervalle ISO\_8601 jusqu’à 2D (minimum 1 minute). Par défaut : 1 heure. |
| maxDeliveryCount | Nombre de remises maximal pour les files d’attente par appareil cloud-à-appareil | 1 à 100. Par défaut : 10. |
| feedback.ttlAsIso8601 | Rétention des messages de commentaires liés au service. | Intervalle ISO\_8601 jusqu’à 2D (minimum 1 minute). Par défaut : 1 heure. |
| feedback.maxDeliveryCount | Nombre de remises maximal pour la file d’attente de commentaires. | 1 à 100. Par défaut : 100. |

Pour plus d’informations, consultez [Gérer des IoT Hubs][lnk-manage].

## Quotas et limitation <a id="throttling"></a>

Chaque abonnement Azure peut avoir au maximum 10 hubs IoT.

Chaque IoT Hub est configuré avec un certain nombre d’unités dans une référence SKU spécifique (pour plus d’informations, consultez [Tarification Azure IoT Hub][lnk-pricing]). La référence et le nombre d’unités déterminent le quota quotidien maximal de messages que vous pouvez envoyer.

La référence détermine également le seuil de limitation qu’IoT Hub applique sur les opérations.

### Limitations d’opérations

Les limitations d’opération sont les limites de taux qui sont appliquées dans les plages de minutes et sont destinées à éviter les abus. IoT Hub essaie d’éviter le renvoi d’erreurs chaque fois que c’est possible, mais les exceptions commencent à être renvoyées si la limitation est dépassée pendant trop longtemps.

Vous trouverez ci-dessous la liste des limitations appliquées. Les valeurs font référence à un hub individuel.

| Limitation | Valeur par hub |
| -------- | ------------- |
| Opérations de registre des identités (création, récupération, création de listes, mise à jour, suppression) | 100/min/unité, jusqu’à 5 000/min |
| Connexions d’appareils | 120/sec/unité (pour S2), 12/sec/unité (pour S1). <br/>Minimum de 100/s. <br/> Par exemple, deux unités S1 équivalent à 2*12 = 24/s, mais vous obtenez au moins 100/s sur vos unités. Avec neuf unités S1, vous obtenez 108/sec (9*12) sur vos unités. |
| Envois de messages appareil-à-cloud | 120/sec/unité (pour S2), 12/sec/unité (pour S1). <br/>Minimum de 100/s. <br/> Par exemple, deux unités S1 équivalent à 2*12 = 24/s, mais vous obtenez au moins 100/s sur vos unités. Avec neuf unités S1, vous obtenez 108/sec (9*12) sur vos unités. |
| Envois cloud-à-appareil | 100/min/unité |
| Réceptions cloud-à-appareil | 1 000/min/unité |

Il est important de préciser que la limitation des *connexions d’appareil* régit la fréquence à laquelle les nouvelles connexions d’appareil peuvent être établies avec un IoT Hub et pas le nombre maximal d’appareils connectés simultanément. La limitation dépend du nombre d’unités qui sont configurées pour le hub.

Par exemple, si vous achetez une seule unité S1, vous obtenez une limitation de 100 connexions par seconde. Cela signifie que pour connecter 100 000 appareils, au moins 1 000 secondes sont nécessaires (environ 16 minutes). Toutefois, vous pouvez avoir autant d’appareils connectés simultanément que d’appareils enregistrés dans le registre d’identité de l’appareil.

Le billet de blog [Limitation d’IoT Hub et vous][lnk-throttle-blog] fournit une présentation détaillée du comportement de limitation d’IoT Hub.

**Remarque**. À tout moment, il est possible d’augmenter les quotas ou les limites en augmentant le nombre d’unités approvisionnées dans un hub IoT.

**Important** : les opérations de registre des identités sont prévues pour une utilisation au moment de l’exécution dans les scénarios de gestion et d’approvisionnement des appareils. La lecture ou la mise à jour d’un grand nombre d’identités d’appareils sont prises en charge via les [tâches d’importation/exportation](#importexport).

## Étapes suivantes

Maintenant que vous disposez d’une vue d’ensemble du développement IoT Hub, suivez les liens ci-après pour en savoir plus :

- [Prise en main de concentrateurs IoT Hubs (didacticiel).][lnk-get-started]
- [Compatibilité des plateformes de système d’exploitation et du matériel][lnk-compatibility]
- [Centre de développement Azure IoT][lnk-iotdev]
- [Conception de votre solution][lnk-guidance]

[Event Hubs - Hôte du processeur d’événements]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[portail Azure]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: iot-hub-tested-configurations.md
[lnk-apis-sdks]: iot-hub-sdks-summary.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/

<!---HONumber=AcomDC_0511_2016-->