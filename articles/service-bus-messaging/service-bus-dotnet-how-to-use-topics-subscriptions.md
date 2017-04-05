---
title: Utilisation des rubriques Azure Service Bus avec .NET | Microsoft Docs
description: "Découvrez comment utiliser les rubriques et abonnements Service Bus avec .NET dans Azure. Les exemples de code sont écrits pour les applications .NET."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Utilisation des rubriques et abonnements Service Bus
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article explique comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en C# et utilisent les API .NET. Les scénarios couverts incluent la création de rubriques et d’abonnements, la création de filtres d’abonnement, l’envoi de messages à une rubrique, la réception de messages en provenance d’un abonnement, et enfin la suppression de rubriques et d’abonnements. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configuration de l'application pour l'utilisation de Service Bus
Lorsque vous créez une application qui fait appel à Service Bus, vous devez ajouter une référence à l'assembly Service Bus et inclure les espaces de noms correspondants. Le plus simple consiste à télécharger le package [NuGet](https://www.nuget.org) approprié.

## <a name="get-the-service-bus-nuget-package"></a>Obtention du package NuGet Service Bus
Le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) constitue le moyen le plus simple de configurer votre application avec toutes les dépendances Service Bus nécessaires. Pour installer le package NuGet Service Bus dans votre projet, procédez comme suit :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis cliquez sur **Gérer les packages NuGet**.
2. Cliquez sur **Parcourir**, recherchez « Azure Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez la boîte de dialogue :
   
   ![][7]

Vous êtes maintenant en mesure d’écrire du code pour Service Bus.

## <a name="create-a-service-bus-connection-string"></a>Création d’une chaîne de connexion Service Bus
Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d’identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, au lieu de la coder en dur :

* Quand vous utilisez les services Azure, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers .csdef et .cscfg).
* Quand vous utilisez les sites Web Azure ou Microsoft Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, le fichier Web.config).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, comme indiqué plus loin dans cet article.

### <a name="configure-your-connection-string"></a>Configuration de votre chaîne de connexion
Le mécanisme de configuration de service vous permet de modifier les paramètres de configuration de façon dynamique depuis le [portail Azure][Azure portal], sans avoir à redéployer votre application. Par exemple, ajoutez une étiquette `Setting` à votre fichier de définition de service (**.csdef**), comme indiqué dans l’exemple suivant.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Vous devez ensuite spécifier des valeurs dans le fichier de configuration de service (.cscfg).

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Utilisez le nom et les valeurs de clé de signature d’accès partagé (SAP) récupérés sur le portail, comme décrit précédemment.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurer votre chaîne de connexion dans le cadre de l’utilisation de sites web Azure ou Machines virtuelles Azure
Quand vous utilisez sites web ou Machines virtuelles, il est recommandé d’utiliser le système de configuration .NET (par exemple, Web.config). Stockez la chaîne de connexion en utilisant l’élément `<appSettings>`.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Utilisez les valeurs de nom et de clé SAP que vous avez récupérées sur le [portail Azure][Azure portal], comme décrit précédemment.

## <a name="create-a-topic"></a>Création d'une rubrique
Vous pouvez effectuer des opérations de gestion sur les rubriques et les abonnements Service Bus à l’aide de la classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Cette classe fournit des méthodes pour créer, énumérer et supprimer des rubriques.

Dans l’exemple suivant, un objet `NamespaceManager` est construit à l’aide de la classe Azure `CloudConfigurationManager` avec une chaîne de connexion constituée de l’adresse de base d’un espace de noms Service Bus et des informations d’identification SAP appropriées assorties d’autorisations pour les gérer. Cette chaîne de connexion se présente comme suit :

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilisez l’exemple suivant en reprenant les paramètres de configuration de la section précédente.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Les surcharges de la méthode [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager) vous permettent de paramétrer les propriétés de la rubrique, par exemple, pour faire en sorte que la valeur par défaut de durée de vie « time-to-live » (TTL) soit appliquée aux messages envoyés à la rubrique. Ces paramètres sont appliqués à l’aide de la classe [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription). L’exemple suivant montre comment créer une rubrique **TestTopic** d’une taille maximale de 5 Go et un message TTL par défaut de 1 minute.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> Vous pouvez utiliser la méthode [TopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) sur les objets [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) pour vérifier s’il existe déjà une rubrique portant un certain nom dans un espace de noms.
> 
> 

## <a name="create-a-subscription"></a>Création d’un abonnement
Vous pouvez également créer des abonnements de rubrique à l’aide de la classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

> [!IMPORTANT]
> Pour que les messages soient reçus par un abonnement, vous devez créer cet abonnement avant d’envoyer des messages vers la rubrique. S’il n’y a aucun abonnement à une rubrique, cette dernière supprime ces messages.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Création d’un abonnement avec le filtre par défaut (MatchAll)
Si aucun filtre n’est spécifié lors de la création d’un abonnement, le filtre **MatchAll** est utilisé par défaut. Lorsque vous utilisez le filtre **MatchAll**, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. Dans l’exemple suivant, l’abonnement « AllMessages » qui est créé utilise le filtre par défaut **MatchAll**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Création d’abonnements avec des filtres
Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Le type de filtre le plus flexible pris en charge par les abonnements est la classe [SqlFilter][SqlFilter] qui implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d’informations sur les expressions utilisables avec un filtre SQL, consultez la syntaxe de [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

L’exemple suivant crée un abonnement **HighMessages** avec un objet [SqlFilter][SqlFilter] qui ne sélectionne que les messages dont la propriété **MessageNumber** personnalisée a une valeur supérieure à 3.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageId > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

De même, l’exemple suivant crée un abonnement appelé **LowMessages** avec un filtre [SqlFilter][SqlFilter] qui ne sélectionne que les messages dont la propriété **MessageNumber** a une valeur inférieure ou égale à 3.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageId <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

À présent, dès lors qu’un message est envoyé à `TestTopic`, il est toujours remis aux destinataires abonnés à l’abonnement de rubrique **AllMessages** et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique **HighMessages** et **LowMessages** (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
Pour envoyer un message à une rubrique Service Bus, votre application crée un objet [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) à l’aide de la chaîne de connexion.

Le code suivant montre comment créer un objet [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) pour la rubrique **TestTopic** créée précédemment à l’aide de l’API [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_).

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Les messages envoyés aux rubriques Service Bus sont des instances de la classe [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Les objets **BrokeredMessage** possèdent un ensemble de propriétés standard (telles que [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) et [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l’objet **BrokeredMessage** ; le sérialiseur **DataContractSerializer** approprié est alors utilisé pour sérialiser l’objet. L’autre possibilité consiste à fournir un objet **System.IO.Stream**.

L’exemple suivant montre comment envoyer cinq messages de test à l’objet **TestTopic** [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) obtenu dans l’exemple de code précédent. Notez que la valeur de la propriété [MessageNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) de chaque message varie selon l’itération de la boucle (ce qui détermine les abonnements qui le reçoivent).

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go. Si le partitionnement est activé, la limite supérieure est plus élevée. Pour plus d’informations, voir [Entités de messagerie partitionnées](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Réception des messages d'un abonnement
Le moyen recommandé pour recevoir les messages d’un abonnement consiste à utiliser un objet [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient). Les objets **SubscriptionClient** peuvent fonctionner dans deux modes différents : [*ReceiveAndDelete* et *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** est la valeur par défaut.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : lorsque Service Bus reçoit une demande de lecture pour un message figurant dans un abonnement, il marque le message comme étant consommé et le renvoie à l’application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d’un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Étant donné que Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

En mode **PeekLock** (le mode par défaut), le processus de réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) pour le message reçu. Lorsque Service Bus obtient l’appel **Complete**, il marque le message comme consommé et le supprime de l’abonnement.

L’exemple suivant montre comment les messages peuvent être reçus et traités à l’aide du mode par défaut **PeekLock** . Pour spécifier une valeur différente pour [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode), vous pouvez utiliser une autre surcharge pour [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_). Cet exemple utilise le rappel [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) pour traiter les messages dès leur arrivée dans l’abonnement **HighMessages**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Cet exemple configure le rappel [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) à l’aide d’un objet [OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions). [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) est défini sur **false** pour permettre le contrôle manuel du moment de l’appel de [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) sur le message reçu. [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) est défini sur 1 minute. Le client attend donc jusqu’à une minute avant de mettre fin au renouvellement et effectue un nouvel appel pour rechercher les messages. La valeur de cette propriété permet de réduire le nombre de fois où le client effectue des appels facturables qui ne récupèrent aucun message.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d’application et des messages illisibles
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle peut appeler la méthode [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__) pour le message reçu (au lieu de la méthode [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Cela amène Service Bus à déverrouiller le message dans l’abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans un abonnement est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’émission de la demande [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), le message est à nouveau remis à l’application lorsqu’elle redémarre. Cette opération est souvent appelée *Au moins une fois*. Chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) du message, qui reste constante pendant les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Suppression de rubriques et d'abonnements
L’exemple suivant montre comment supprimer la rubrique **TestTopic** de l’espace de noms de service **HowToSample**.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

La suppression d’une rubrique a également pour effet de supprimer les abonnements inscrits dans la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l’abonnement **HighMessages** de la rubrique **TestTopic**.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les principes de base des rubriques et des abonnements Service Bus, nous vous invitons à consulter les liens suivants pour en savoir plus :

* [Files d’attente, rubriques et abonnements][Queues, topics, and subscriptions].
* [Topic filters sample (Exemple de filtres de rubrique)][Topic filters sample]
* Référence d’API pour [SqlFilter][SqlFilter].
* Créez une application opérationnelle qui échange des messages avec une file d’attente Service Bus : [Didacticiel .NET sur la messagerie répartie Service Bus][Service Bus brokered messaging .NET tutorial].
* Exemples Service Bus : disponibles en téléchargement depuis la page [Exemples Azure][Azure samples] ou en consultation dans la [présentation](service-bus-samples.md).

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

