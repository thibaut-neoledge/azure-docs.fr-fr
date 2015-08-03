<properties 
   pageTitle="Présentation du modèle de sécurité et de l'authentification des hubs d'événements"
   description="FAQ sur les hubs d'événements"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="06/09/2015"
   ms.author="sethm" />

# Présentation du modèle de sécurité et de l'authentification des hubs d'événements

Le modèle de sécurité du hub d'événements remplit les conditions suivantes :

- Seuls les appareils qui présentent des informations d'identification valides peuvent envoyer des données à un hub d'événements.

- Un appareil ne peut pas emprunter l'identité d'un autre appareil.

- Il est possible d'empêcher un appareil non fiable d'envoyer des données à un hub d'événements.

## Authentification des appareils

Le modèle de sécurité du hub d'événements est basé sur une combinaison de jetons de [signature d'accès partagé (SAS)](https://msdn.microsoft.com/library/dn170477.aspx) et d'éditeurs d'événements. Un éditeur d'événements définit un point de terminaison virtuel pour un hub d'événements. L'éditeur ne peut être utilisé que pour envoyer des messages à un hub d'événements. Il n'est pas possible de recevoir des messages à partir de l'éditeur.

En règle générale, un hub d'événements utilise un seul éditeur par appareil. Tous les messages qui sont envoyés à un éditeur d'un hub d'événements sont empilés dans celui-ci. Les éditeurs permettent un contrôle d'accès précis et une limitation.

Un jeton unique, qui est téléchargé sur l'appareil, est affecté à chaque appareil. Les jetons sont produits de façon à ce que chaque jeton unique donne accès à un éditeur unique différent. Un appareil qui possède un jeton ne peut envoyer qu'à un seul éditeur, mais pas un autre éditeur. Si plusieurs appareils partagent le même jeton, alors ils partagent un éditeur.

Bien que cela soit déconseillé, il est possible d'équiper les appareils de jetons qui donnent un accès direct à un hub d'événements. N'importe quel appareil qui contient ce type de jeton peut envoyer des messages directement dans ce hub d'événements. Un appareil de ce type ne sera pas soumis à la limitation. En outre, il est impossible d'empêcher l'appareil d'effectuer des envois à ce hub d'événements.

Tous les jetons sont signés avec une clé SAS. En règle générale, tous les jetons sont signés avec la même clé. Les appareils ne sont pas conscients de la clé. Cela empêche les appareils de fabriquer des jetons.

### Création de la clé

Lorsque vous créez un espace de noms, Service Bus génère une clé SAS de 256 bits nommée **RootManageSharedAccessKey**. Cette clé accorde les droits d'envoi, d'écoute et de gestion pour l'espace de noms. Vous pouvez créer des clés supplémentaires. Nous vous recommandons de produire une clé qui accorde les droits d'envoi au hub d'événements spécifique. Pour le reste de cette rubrique, supposons que vous avez nommé cette clé `EventHubSendKey`.

L'exemple suivant crée une clé d'envoi uniquement lors de la création du hub d'événements :

```C#

// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that allows sending to that Event Hub.
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);

```

### Génération de jetons

Vous pouvez générer des jetons à l'aide de la clé SAS. Vous ne devez produire qu'un seul jeton par appareil. Les jetons peuvent ensuite être générés à l'aide de la méthode suivante. Tous les jetons sont générés à l'aide de la clé **EventHubSendKey**. Une URI unique est affectée à chaque jeton.

	public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)

Lors de l'appel de cette méthode, l'URI doit être spécifiée en tant que `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Pour tous les jetons, l'URI est identique, à l'exception de `PUBLISHER_NAME`, qui doit être différent pour chaque jeton. Dans l'idéal, `PUBLISHER_NAME` représente l'ID de l'appareil qui reçoit ce jeton.

Cette méthode génère un jeton avec la structure suivante :

	SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}

L'heure d'expiration du jeton est exprimée en secondes à partir du 1er janvier 1970. Vous trouverez ci-dessous un exemple de jeton :

	SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey

En règle générale, les jetons ont une durée de vie équivalente ou supérieure à la durée de vie de l'appareil. Si l'appareil a la possibilité d'obtenir un nouveau jeton, il est possible d'utiliser des jetons avec une durée de vie plus courte.

### Envoi de données par les appareils

Une fois que les jetons ont été créés, chaque appareil est configuré avec son propre jeton unique.

Lorsque l'appareil envoie des données dans un hub d'événements, il balise son jeton avec la requête d'envoi. Pour empêcher un intrus de procéder à des écoutes clandestines et de voler le jeton, la communication entre l'appareil et le hub d'événements doit avoir lieu sur un canal chiffré.

### Inscription des appareils sur liste noire

Si un jeton est volé par un intrus, celui-ci peut emprunter l'identité de l'appareil dont le jeton a été volé. L'inscription d'un appareil sur liste noire le rend inutilisable jusqu'à ce qu'il reçoive un nouveau jeton qui utilise un autre éditeur.

## Authentification des applications principales

Pour authentifier des applications principales qui consomment les données générées par les appareils, les hubs d'événements utilisent un modèle de sécurité qui est similaire au modèle utilisé pour les rubriques Service Bus. Un groupe de consommateurs de hubs d'événements est équivalent à un abonnement à une rubrique Service Bus. Un client peut créer un groupe de consommateurs si la requête de création du groupe de consommateurs est accompagnée d'un jeton qui accorde des droits de gestion pour le hub d'événements ou pour l'espace de noms auquel appartient le hub d'événements. Un client est autorisé à consommer des données à partir d'un groupe de consommateurs si la requête de réception est accompagnée d'un jeton qui accorde des droits de réception pour ce groupe de consommateurs, le hub d'événements ou l'espace de noms auquel appartient le hub d'événements.

La version actuelle de Service Bus ne prend pas en charge les règles SAS pour les abonnements individuels. Il en va de même pour les groupes de consommateurs de hubs d'événements. La prise en charge SAS sera ajoutée ultérieurement pour ces deux fonctionnalités.

En l'absence d'authentification SAS pour les groupes de consommateurs individuels, vous pouvez utiliser des clés SAS pour sécuriser tous les groupes de consommateurs avec une clé commune. Cette approche permet à une application de consommer des données à partir de n'importe quel groupe de consommateurs d'un hub d'événements.

### Création d'identités de service, de parties de confiance et de règles dans ACS

ACS prend en charge plusieurs façons de créer des identités de service, des parties de confiance et des règles, mais la méthode la plus simple est d'utiliser [SBAZTool](http://code.msdn.microsoft.com/windowsazure/Authorization-SBAzTool-6fd76d93). Par exemple :

1. Créer une identité de service pour un **EventHubSender**. Ceci renvoie le nom de l'identité de service qui a été créée et sa clé :

		sbaztool.exe exe -n <namespace> -k <key>  makeid eventhubsender

2. Accorder « Envoyer les revendications » **EventHubSender** au hub d'événements :

		sbaztool.exe -n <namespace> -k <key> grant Send /AuthTestEventHub eventhubsender

3. Créer une identité de service pour un récepteur au groupe de consommateurs 1 :

		sbaztool.exe exe -n <namespace> -k <key> makeid consumergroup1receiver

4. Accorder « Écouter les revendications » `consumergroup1receiver` au **groupe de consommateurs 1** :

		sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup1 consumergroup1receiver

5. Créer une identité de service pour un récepteur au **groupe de consommateurs 2** :

		sbaztool.exe exe -n <namespace> -k <key>  makeid consumergroup2receiver

6. Accorder « Écouter les revendications » `consumergroup2receiver` au **groupe de consommateurs 2** :

		sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup2 consumergroup2receiver

## Étapes suivantes

Pour plus d'informations sur les hubs d'événements, consultez les rubriques suivantes :

- [Vue d'ensemble des hubs d'événements]
- Un [exemple d'application complet qui utilise des hubs d'événements].
- Une [solution de messages de file d'attente] utilisant les files d'attente Service Bus.

[Vue d'ensemble des hubs d'événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d'événements]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[solution de messages de file d'attente]: ../cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=July15_HO4-->