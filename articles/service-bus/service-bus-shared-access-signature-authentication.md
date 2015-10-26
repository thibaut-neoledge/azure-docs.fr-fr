<properties 
   pageTitle="Authentification par signature d’accès partagé avec Service Bus | Microsoft Azure"
   description="Plus d’informations sur l’authentification SAP avec Service Bus."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="sethm" />

# Authentification par signature d’accès partagé avec Service Bus

L’authentification par [Signature d’accès partagé (SAP)](service-bus-sas-overview.md) aux applications de s’authentifier auprès de Service Bus à l’aide d’une clé d’accès sur l’espace de noms, ou sur l’entité de messagerie (file d’attente ou rubrique) avec des droits spécifiques associés. Vous pouvez ensuite utiliser cette clé pour générer un jeton SAS que les clients peuvent ensuite utiliser pour s’authentifier auprès de Service Bus.

La prise en charge de l’authentification SAP est incluse dans le kit de développement logiciel SDK Windows Azure SDK version 2.0 et ultérieure. Pour plus d’informations sur l’authentification Service Bus, consultez [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md).

## Concepts

L’authentification SAP dans Service Bus implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource Service Bus. Les clients demandent l’accès aux ressources de Service Bus à l’aide d’un jeton SAS. Ce jeton se compose de l’URL de ressource à laquelle on accède et de l’expiration signée avec la clé configurée.

Vous pouvez configurer des règles d’autorisation de Signature d’accès partagé sur les [relais](service-bus-fundamentals-hybrid-solutions.md/#relays), les [files d’attente](service-bus-fundamentals-hybrid-solutions.md/#queues), les [rubriques](service-bus-fundamentals-hybrid-solutions.md/#topics), et les [concentrateurs d’événements](https://azure.microsoft.com/documentation/services/event-hubs/) Service Bus.

L’authentification SAP utilise les éléments suivants :

- [Règle d’autorisation d’accès partagée](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) : clé de chiffrement principale cryptée en Base64 à 256 bits, une clé secondaire facultative et un nom de clé et des droits associés (une collection de droits *écouter*, *envoyer* ou *gérer*).

- Jeton de [signature d’accès partagé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx) : généré à l’aide de la HMAC-SHA256 d’une chaîne de ressource, constitué de l’URI de la ressource accessible et à échéance, avec la clé de chiffrement. La signature et d’autres éléments décrits dans les sections suivantes sont formatées en chaîne pour constituer le jeton [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx).

## Configuration de l’authentification de signature d’accès partagé

Vous pouvez configurer la règle [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) sur les espaces de noms, les files d’attente ou rubriques Service Bus. La configuration d’un abonnement [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) sur un abonnement Service Bus n’est pas pris en charge pour l’instant, mais vous pouvez utiliser les règles configurées sur un espace de noms ou une rubrique permettant de sécuriser l’accès aux abonnements. Pour obtenir un exemple fonctionnel qui illustre cette procédure, consultez l’exemple [Utilisation de l’authentification de signature d’accès partagé (SAS) avec les abonnements Service Bus](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c).

Un maximum de 12 règles de ce type peut être configuré sur un espace de noms, une file d’attente ou une rubrique Service Bus. Les règles qui sont configurées sur un espace de noms Service Bus s’appliquent à toutes les entités appartenant à cet espace de noms.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

Dans cette figure, les règles d’autorisation *manageRuleNS*, *sendRuleNS* et *listenRuleNS* s’appliquent à la file d’attente Q1 et à la rubrique T1, tandis que *listenRuleQ* et *sendRuleQ* s’appliquent uniquement à la file d’attente Q1 et *sendRuleT* s’applique uniquement à la rubrique T1.

Les paramètres clés de [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) sont les suivantes :

|Paramètre|Description|
|---|---|
|*Nom de clé*|Chaîne qui décrit la règle d’autorisation.|
|*Clé primaire*|Clé principale cryptée en Base64 sur 256 bits pour signer et valider le jeton SAS.|
|*Clé secondaire*|Clé secondaire cryptée en Base64 codée sur 256 bits pour signer et valider le jeton SAS.|
|*Droits d’accès*|Liste des droits d’accès accordés par la règle d’autorisation. Ces droits peuvent être n’importe quel ensemble contenant les droits d’écoute, d’envoi et de gestion.|

Quand un espace de noms Service Bus est configuré, un objet [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) avec [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) défini sur **RootManageSharedAccessKey** est créé par défaut. Deux objets [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) sont également configurés pour les concentrateurs de notification : un avec les droits écouter, envoyer et gérer et l’autre avec les droits écouter uniquement.

## Régénérez et révoquer les clés pour les règles d’autorisations d’accès partagé

Il est recommandé de régénérer régulièrement les clés utilisées dans l’objet [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Les applications doivent généralement utiliser la [Clé primaire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) pour générer un jeton SAS. Lors de la régénération des clés, vous devez remplacer la [clé secondaire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) par l’ancienne clé primaire, et générer une nouvelle clé en tant que nouvelle clé primaire. Cela vous permet de continuer à utiliser des jetons d’autorisation n’ayant pas encore expiré émis avec l’ancienne clé primaire.

Si une clé est compromise, vous devez révoquer les clés, vous pouvez régénérer la [clé primaire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) et la [clé secondaire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) d’une règle [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), et les remplacer par de nouvelles clés. Cette procédure annule tous les jetons signés avec les anciennes clés.

## Génération d’un jeton de signature d’accès partagé

Tout client a accès aux clés de connexion spécifiées dans la règle d’autorisation d’accès partagée et peut générer un jetons SAP. Il est mis en forme comme suit :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

La **signature** du jeton de sécurité SAP est calculé à l’aide du hachage HMAC-SHA256 d’une chaîne de connexion avec la propriété [Clé primaire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) d’une règle d’autorisation. La chaîne de signature se compose d’une URI de ressource et d’expiration, au format suivant :

```
StringToSign = <resourceURI> + "\n" + expiry;
```

Notez que vous devez utiliser l’URI de ressource codé pour cette opération. L’URI de ressource est l’URI complet de la ressource Service Bus à laquelle vous souhaitez accéder. Par exemple, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>` ; qui est, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

L’échéance est représentée par le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970.

La règle de l’autorisation d’accès partagé utilisée pour la signature doit être configurée sur l’entité spécifiée par cette URI, ou par un de ses parents hiérarchiques. Par exemple, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` dans l’exemple précédent.

Un jeton SAS est valable pour toutes les ressources du `<resourceURI>` utilisées dans la chaîne de signature.

Le [Nom de clé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) présent dans le jeton SAS fait référence à la règle d’autorisation partagée **Nom de clé** utilisée pour générer le jeton.

L’*URL-encoded-resourceURI* doit être identique à l’URI utilisée dans la chaîne de signature lors du traitement de la signature. Elle doit être [encodée en pourcentage](https://msdn.microsoft.com/library/4fkewx0t.aspx).

## Comment utiliser l’authentification par signature d’accès partagé avec Service Bus

Les scénarios suivants incluent la configuration des règles d’autorisation, la génération de jetons SAP et l’autorisation de client.

Pour visionner un exemple fonctionnel d’application Service Bus qui illustre la configuration et l’autorisation SAP, consultez [Authentification de la Signature d’accès partagé avec Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Un exemple d’utilisation de règles d’autorisation SAP configuré sur les espaces de noms ou des rubriques pour sécuriser les abonnements Service Bus est disponible ici : [Utilisation de l’authentification de signature d’accès partagé (SAP) avec les abonnements Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## Accès aux règles d’autorisation d’accès partagé sur un espace des noms

Les opérations sur la racine de la racine d’espace de noms Service Bus requièrent l’authentification par certificat. Vous devez télécharger un certificat de gestion correspondant à votre abonnement Pour télécharger un certificat de gestion, cliquez sur **Paramètres** dans le volet de gauche du portail Azure. Pour plus d’informations sur la création de certificats, consultez la page [Création d’un certificat de gestion pour Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).

Le point de terminaison pour l’accès aux règles d’autorisation de l’accès partagé dans un espace de noms Service Bus est la suivante :

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Pour créer un objet [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) dans un espace de noms Service Bus, exécutez une opération POST sur ce point de terminaison avec les informations de règle sérialisée JSON ou XML. Par exemple :

```
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

De même, utilisez une opération GET sur le point de terminaison pour lire les règles d’autorisation configurées sur l’espace de noms.

Pour mettre à jour ou supprimer une règle d’autorisation spécifique, utilisez le point de terminaison suivant :

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## Accès aux règles d’autorisation de d’accès partagé sur une entité

Vous pouvez accéder à un objet [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) configuré sur une file d’attente Service Bus ou une rubrique via le regroupement [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) dans la description [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) , [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) ou [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) correspondante.

Le code suivant montre comment ajouter des règles d’autorisation à une file d’attente.

```
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString( 
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey", 
    SharedAccessAuthorizationRule.GenerateRandomKey(), 
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## Problème d’autorisation de la signature d’accès partagé (SAP)

Les applications utilisant le kit de développement logiciel (SDK) avec les bibliothèques.NET Service BUS peuvent utiliser les autorisations SAP via la classe [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx) classe. Le code suivant montre comment utiliser le fournisseur de jeton pour envoyer des messages à une file d’attente Service Bus.

```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb", 
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri, 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Les applications peuvent également utiliser des associations de sécurité pour l’authentification en utilisant une chaîne de connexion dans les méthodes qui acceptent des chaînes de connexion.

Pour utiliser une autorisation SAS avec les relais Service Bus, vous pouvez utiliser des clés SAS configurées sur un espace de noms Service Bus. Si vous créez explicitement un relais sur l’espace de noms ([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) avec un objet [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)), vous pouvez définir les règles SAP pour ce relais. Pour utiliser une autorisation SAS avec des abonnements Service Bus, vous pouvez utiliser des clés SAS configurées sur un espace de noms Service Bus ou sur une rubrique.

## Droits requis pour les opérations Service Bus

Le tableau suivant affiche les droits d’accès requis pour effectuer diverses opérations sur les ressources Service Bus.

|Opération|Revendication obligatoire|Étendue de la revendication|
|---|---|---|
|**Espace de noms**|||
|Configure une règle d’autorisation dans un espace de noms|Gérer|N’importe quelle adresse d’espace de noms|
|**Registre de service**|||
|Énumération des stratégies privées|Gérer|N’importe quelle adresse d’espace de noms|
|Relais|||
|Commencer à écouter sur un espace de noms|Écouter|N’importe quelle adresse d’espace de noms|
|Envoyer des messages à un écouteur sur un espace de noms|Envoyer|N’importe quelle adresse d’espace de noms|
|**File d'attente**|||
|Création d’une file d’attente|Gérer|N’importe quelle adresse d’espace de noms|
|Suppression d'une file d'attente|Gérer|N’importe quelle adresse de file d’attente valide|
|Énumérer les files d’attente|Gérer|/$Resources/Queues|
|Obtenir la description de file d’attente|Gérer ou envoyer|N’importe quelle adresse de file d’attente valide|
|Configure une règle d’autorisation pour une file d’attente|Gérer|N’importe quelle adresse de file d’attente valide|
|Envoyer dans la file d’attente|Envoyer|N’importe quelle adresse de file d’attente valide|
|Réception des messages d'une file d'attente|Écouter|N’importe quelle adresse de file d’attente valide|
|Abandonner ou terminer des messages après la réception du message en mode de verrouillage|Écouter|N’importe quelle adresse de file d’attente valide|
|Différer un message pour une récupération ultérieure|Écouter|N’importe quelle adresse de file d’attente valide|
|Mettre un message au rebut|Écouter|N’importe quelle adresse de file d’attente valide|
|Obtenir l’état associé à une session de file d’attente|Écouter|N’importe quelle adresse de file d’attente valide|
|Obtenir l’état associé à une session de file d’attente de message|Écouter|N’importe quelle adresse de file d’attente valide|
|**Rubrique**|||
|Création d'une rubrique|Gérer|N’importe quelle adresse d’espace de noms|
|Supprimer une rubrique|Gérer|N’importe quelle adresse de rubrique valide|
|Énumérer les rubriques|Gérer|/$Resources/Topics|
|Obtenir la description de la rubrique|Gérer ou envoyer|N’importe quelle adresse de rubrique valide|
|Configure une règle d’autorisation pour une rubrique|Gérer|N’importe quelle adresse de rubrique valide|
|Envoyer à la rubrique|Envoyer|N’importe quelle adresse de rubrique valide|
|**Abonnement**|||
|Création d'un abonnement|Gérer|N’importe quelle adresse d’espace de noms|
|Supprimer l’abonnement|Gérer|../myTopic/Subscriptions/mySubscription|
|Énumérer les abonnements|Gérer|../myTopic/Subscriptions|
|Obtenir la description de l’abonnement|Gérer ou écouter|../myTopic/Subscriptions/mySubscription|
|Abandonner ou terminer des messages après la réception du message en mode de verrouillage|Écouter|../myTopic/Subscriptions/mySubscription|
|Différer un message pour une récupération ultérieure|Écouter|../myTopic/Subscriptions/mySubscription|
|Mettre un message au rebut|Écouter|../myTopic/Subscriptions/mySubscription|
|Obtenir l’état associé à une session de rubrique|Écouter|../myTopic/Subscriptions/mySubscription|
|Définir l’état associé à une session de rubrique|Écouter|../myTopic/Subscriptions/mySubscription|
|**Règle**|||
|Créer une règle|Gérer|../myTopic/Subscriptions/mySubscription|
|Supprimer une règle|Gérer|../myTopic/Subscriptions/mySubscription|
|Énumérer des règles|Gérer ou écouter|.. /myTopic/Subscriptions/mySubscription/Rules|
|**Notification Hubs**|||
|Création d’un concentrateur de notifications|Gérer|N’importe quelle adresse d’espace de noms|
|Créer ou mettre à jour l’inscription d’un appareil actif|Écouter ou gérer|.. /notificationHub/balises / {tag} / des enregistrements|
|Mettre à jour les informations PNS|Écouter ou gérer|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|Création d’un concentrateur de notification|Envoyer|../notificationHub/messages|

## Étapes suivantes

Pour obtenir une vue d’ensemble de SAP dans Service Bus, consultez [Signatures d’accès partagé](service-bus-sas-overview.md).

Pour plus de contexte au sujet de l’authentification Service Bus, consultez [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md).

<!---HONumber=Oct15_HO3-->