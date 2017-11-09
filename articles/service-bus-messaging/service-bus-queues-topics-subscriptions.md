---
title: "Présentation des files d’attente de messagerie, des rubriques et des abonnements Azure Service Bus | Microsoft Docs"
description: "Présentation des entités de messagerie Service Bus"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm
ms.openlocfilehash: 00f9f38fbae028486270053dedb4df580a3f1a44
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Files d’attente, rubriques et abonnements Service Bus

Microsoft Azure Service Bus prend en charge un ensemble de technologies middleware Cloud orientées messages, notamment la mise en file d’attente de messages fiable et la messagerie de publication/abonnement durable. Ces fonctionnalités de messagerie répartie peuvent être considérées comme des fonctions de messagerie découplée prenant en charge le découplage temporel de publication-souscription, l’abonnement de messagerie temporel découplage, les scénarios d’infrastructure d’équilibrage de charge à l’aide de la structure de messagerie Service Bus. La communication découplée présente de nombreux avantages ; par exemple, les clients et les serveurs peuvent se connecter en fonction des besoins et exécuter leurs opérations de manière asynchrone.

Les entités de messagerie qui constituent l’essentiel des fonctionnalités de messagerie dans Service Bus sont les files d’attente, les rubriques et abonnements, les règles et les actions.

## <a name="queues"></a>Files d’attente

Les files d’attente permettent la livraison de messages sur le principe du *premier entré, premier sorti (FIFO)* à un ou plusieurs destinataires concurrents. En d’autres termes, les messages sont généralement prévus pour être reçus et traités par les récepteurs dans l’ordre dans lesquels ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par un seul consommateur de message. Un des principaux avantages de l’utilisation des files d’attente consiste à réaliser le « découplage temporel » des composants d’application. En d’autres termes, les producteurs (expéditeurs) et les consommateurs (récepteurs) ne sont pas forcés d’envoyer et de recevoir des messages simultanément, car les messages sont stockés durablement dans la file d’attente. En outre, le producteur n’a pas à attendre de réponse du destinataire pour continuer de traiter et d’envoyer d’autres messages.

Un des avantages associés est le nivellement de charge, qui permet aux producteurs et aux consommateurs d’envoyer et de recevoir des messages à des vitesses différentes. Dans de nombreuses applications, la charge système varie au fil du temps. Cependant le temps de traitement nécessaire à chaque élément de travail est normalement constant. L’ajout d’une file d’attente entre les producteurs et les consommateurs des messages fait que l’application de destination doit être en mesure de gérer seulement une charge moyenne, plutôt que la charge de travail maximale. La file d’attente s’allonge et se raccourcit en fonction de la charge entrante. Ceci permet de faire des économies concernant les infrastructures nécessaires pour faire face à la charge de travail de l’application. À mesure que la charge augmente, d’autres processus de travail peuvent être ajoutés pour lire les éléments de la file d’attente. Chaque message est traité par un seul des processus de travail. De plus, cet équilibrage de la charge basé sur l’extraction permet d’optimiser l’utilisation des ordinateurs de travail, même si ceux-ci diffèrent en termes de puissance de traitement, car ils demandent les messages au maximum de leur capacité. Ce modèle est souvent appelé modèle consommateur concurrent.

L’utilisation de files d’attente comme intermédiaire entre les producteurs et les consommateurs de message fournit un couplage souple inhérent entre les composants. Producteurs et consommateurs étant indépendants les uns des autres, il est possible de mettre à niveau un consommateur sans que cela affecte le producteur.

La création d’une file d’attente est un processus en plusieurs étapes. Les opérations de gestion d’entités (à la fois des files d’attente et des rubriques) de messagerie Service Bus s’exécutent via la classe [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) générée à l’aide de l’adresse de base de l’espace des noms Service Bus et des informations d’identification de l’utilisateur. La classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Après avoir créé un objet [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) du nom et de la clé SAP, et un objet de gestion d’espaces de noms de service, vous pouvez utiliser la méthode [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) pour créer la file d’attente. Par exemple :

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Vous pouvez ensuite créer un objet de file d’attente et une structure de messagerie avec l’URI de Bus de Service comme argument. Par exemple :

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Vous pouvez envoyer des messages à la file d’attente. Par exemple, si vous avez une liste de messages répartis nommée `MessageList`, le code ressemble à ce qui suit :

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Vous pouvez alors recevoir des messages à partir des files d’attente, comme suit :

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

En mode [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), l’opération est exécutée une seule fois, en d’autres termes, lorsque Service Bus reçoit la demande, il marque ce message comme étant consommé et le renvoie à l’application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d’un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Service Bus ayant marqué le message comme étant consommé, lorsque l’application redémarre et recommence à traiter des messages, elle ignore le message consommé avant l’incident.

En mode [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode), la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas se permettre de manquer des messages. Lorsque Service Bus reçoit la demande, il recherche le message suivant à consommer, le verrouille pour empêcher d’autres consommateurs de le recevoir, puis le renvoie à l’application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) pour le message reçu. Lorsque Service Bus obtient l’appel **Complete**, il marque le message comme étant consommé.

Si l’application est dans l’impossibilité de traiter un message pour une raison quelconque, elle peut appeler la méthode [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) pour le message reçu (au lieu de la méthode [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Cela permet à Service Bus de déverrouiller le message et le met à disposition pour être à nouveau reçu, soit par le même consommateur, soit par un consommateur concurrent. De même, il faut savoir qu’un message verrouillé dans une file d’attente est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, en cas d’incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception (essentiellement en effectuant une opération [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) par défaut).

Notez que si l’application subit un incident après le traitement du message, mais avant l’envoi de la demande **Complete**, le message est à nouveau remis à l’application lorsqu’elle redémarre. Cette opération est souvent appelée *Au moins une fois*. Chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer un traitement dupliqué, une logique supplémentaire est nécessaire dans l’application pour détecter les doublons susceptibles d’être obtenus à partir de la propriété **MessageId** du message, qui demeure constante entre les tentatives de remise. Cette opération est connue sous le nom de traitement *seulement une fois*.

## <a name="topics-and-subscriptions"></a>Rubriques et abonnements
Contrairement aux files d’attente, où chaque message est traité par un seul consommateur, les *rubriques* et les *abonnements* fournissent une forme de communication « un à plusieurs », à l’aide d’un modèle de *publication et d’abonnement*. Utile en cas d’évolution vers un très grand nombre de destinataires, chaque message publié est mis à disposition de tous les abonnements inscrits auprès de la rubrique. Les messages sont envoyés à une rubrique et remis à un ou plusieurs abonnements associés, en fonction des règles de filtrage qui peuvent être définies sur une base par abonnement. Les abonnements peuvent utiliser des filtres supplémentaires pour restreindre les messages qu’ils souhaitent recevoir. Les messages sont envoyés à une rubrique de la même façon qu’ils sont envoyés à une file d’attente, mais les messages ne sont pas reçus directement de la rubrique. Ils sont envoyés depuis les abonnements. Un abonnement à une rubrique ressemble à une file d’attente virtuelle recevant des copies des messages envoyés à la rubrique. Les messages sont reçus d’un abonnement identique de la même façon que ceux qui sont reçus de la file d’attente.

Par comparaison, la fonctionnalité d’envoi de messages d’une file d’attente mappe directement sur une rubrique et sa fonctionnalité de réception de messages est mappé sur un abonnement. Cela signifie, entre autres, que les abonnements prennent en charge des modèles similaires à ceux décrits plus haut dans cette section concernant les files d’attente : consommateurs simultanés, découplage temporel, nivellement et équilibrage de charge.

La création d’une rubrique est similaire à la création d’une file d’attente, comme illustré dans l’exemple de la section précédente. Créez l’URI de service, puis utilisez la classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) pour créer l’espace de noms client. Vous pouvez ensuite créer une rubrique à l’aide de la méthode [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_). Par exemple :

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

ensuite, ajoutez les abonnements comme souhaité :

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

vous pouvez ensuite créer un client de rubrique. Par exemple :

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Avec l’expéditeur du message, vous pouvez envoyer et recevoir des messages vers et à partir de la rubrique, comme indiqué dans la section précédente. Par exemple :

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

À l’instar des files d’attente, les messages sont reçus d’un abonnement à l’aide d’un objet [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) et non d’un objet [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient). Créez le client d’abonnement, en transférant le nom du sujet, le nom de l’abonnement et (éventuellement) le mode de réception en tant que paramètres. Par exemple, avec l’abonnement **Inventaire** :

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Règles et actions
Dans de nombreux scénarios, les messages ayant des caractéristiques spécifiques doivent être traités différemment. Pour ce faire, vous pouvez configurer des abonnements pour rechercher les messages présentant les propriétés souhaitées, puis apporter certaines modifications à ces propriétés. Bien que les abonnements Service Bus voient tous les messages envoyés à la rubrique, vous pouvez uniquement copier un sous-ensemble de ces messages dans la file d’attente d’abonnement virtuelle. Pour ce faire, il faut utiliser des filtres d’abonnement. Ces modifications sont appelées *actions de filtrage*. Lorsqu’un abonnement est créé, vous pouvez fournir une expression de filtre fonctionnant sur les propriétés du message, aussi bien les propriétés du système (par exemple, **Label**) et les propriétés personnalisées de l’application (par exemple, **StoreName**.) L’expression de filtre SQL est facultative dans ce cas ; sans expression de filtre SQL, toute action de filtre définie sur un abonnement sera effectuée sur tous les messages de cet abonnement.

Avec l’exemple précédent, pour filtrer les messages provenant uniquement de **Store1**, vous devez créer l’abonnement Tableau de bord comme suit :

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Avec ce filtre d’abonnement en place, seuls les messages dont la propriété `StoreName` est définie sur `Store1` sont copiés vers la file d’attente virtuelle de l’abonnement `Dashboard`.

Pour plus d’informations sur les valeurs de filtre possibles, consultez la documentation des classes [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) et [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction). Consultez également les exemples [Brokered Messaging: Advanced Filters](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Messagerie répartie : Filtres avancés) et [Topic Filters](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) (Filtres de rubrique).

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques avancées suivantes pour obtenir plus d’informations et des exemples d’utilisation de la messagerie Service Bus.

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Didacticiel .NET sur la messagerie répartie Service Bus](service-bus-brokered-tutorial-dotnet.md)
* [Didacticiel REST sur la messagerie répartie Service Bus](service-bus-brokered-tutorial-rest.md)
* [Topic Filters sample ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) (Exemple de filtres de rubrique)
* [Brokered Messaging: Advanced Filters sample](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Messagerie répartie : exemples de filtres avancés)

