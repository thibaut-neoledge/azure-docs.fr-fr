---
title: "Création d’applications qui utilisent des rubriques et des abonnements Service Bus | Microsoft Docs"
description: Introduction aux fonctions publication-abonnement offertes par les abonnements et les rubriques de Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a48fc9b0-b7b0-464e-8187-a517bf8b4eb4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 799ef33c924a0067bb5e8da9d1b4e50091dbabf6


---
# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Création d’applications qui utilisent des rubriques et des abonnements Service Bus
Azure Service Bus prend en charge un ensemble de technologies interlogicielles Cloud orientées messages, notamment une mise en file d'attente des messages fiable et une messagerie de publication/abonnement durable. Cet article s’appuie sur les informations fournies dans [Création d’applications qui utilisent les files d’attente Service Bus](service-bus-create-queues.md) et présente les fonctionnalités de publication/d’abonnement offertes par les rubriques Service Bus.

## <a name="evolving-retail-scenario"></a>Scénario de vente au détail - La suite
Cet article propose une suite du scénario de vente au détail décrit dans la rubrique [Création d’applications qui utilisent les files d’attente Service Bus](service-bus-create-queues.md). Souvenez-vous. Les données de vente recueillies à partir de terminaux de point de vente (PDV) doivent être acheminées vers un système de gestion des stocks. Celui-ci utilise ces données pour déterminer le moment où il est nécessaire de renouveler les stocks. Chaque terminal de PDV consigne ses données de vente en envoyant des messages à la file d’attente **DataCollectionQueue**, dans laquelle ils sont conservés jusqu’à ce qu’ils soient reçus par le système de gestion des stocks, comme illustré ici :

![Service Bus 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Pour faire évoluer ce scénario, une nouvelle exigence a été ajoutée au système : le propriétaire de la boutique aimerait surveiller les performances de son point de vente en temps réel.

Pour répondre à cette exigence, le système doit puiser des informations dans le flux de données des ventes. Nous souhaitons conserver l'envoi systématique de chaque message provenant des terminaux de PDV vers le système de gestion des stocks, comme c'était le cas auparavant. En revanche, nous souhaitons créer une copie de chaque message pour l'utiliser dans une vue sous forme de tableau de bord à destination du propriétaire du magasin.

Dans tous les cas de ce type, quand chaque message doit être consommé par plusieurs parties, vous pouvez utiliser les *rubriques* Service Bus. Les rubriques fournissent un modèle de publication/d’abonnement, dans lequel chaque message publié est mis à disposition d’un abonnement (ou plusieurs) inscrit auprès de la rubrique. Au contraire, avec les files d'attente, chaque message est reçu par un seul consommateur.

Les messages sont envoyés à une rubrique de la même façon qu’ils sont envoyés à une file d’attente. Toutefois, les messages ne sont pas reçus à partir de la rubrique directement ; ils sont reçus à partir des abonnements. Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique en question. Les messages sont reçus à partir d’un abonnement identique de la même façon que ceux qui sont reçus de la file d’attente.

Pour revenir au scénario de vente au détail, la file d’attente est remplacée par une rubrique et un abonnement est ajouté. Il sera utilisé par le composant du système de gestion des stocks. Le système se présente alors comme suit :

![Service Bus 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configuration ici se comporte comme la conception précédente basée sur la file d'attente. Autrement dit, les messages envoyés à la rubrique sont routés vers l’abonnement **Inventaire**, à partir duquel le **système de gestion des stocks** les consomme.

Pour prendre en charge le tableau de bord de gestion, créons un second abonnement sur la rubrique, comme illustré ici :

![Service Bus 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Avec cette configuration, chaque message provenant des terminaux de PDV est accessible à la fois aux abonnements **Tableau de bord** et **Inventaire**.

## <a name="show-me-the-code"></a>Afficher le code
L’article [Création d’applications qui utilisent les files d’attente Service Bus](service-bus-create-queues.md) décrit comment s’inscrire pour obtenir un compte Azure et créer un espace de noms de service. Pour utiliser l'espace de noms Service Bus, une application doit référencer l'assembly Service Bus, et plus précisément le fichier Microsoft.ServiceBus.dll. Le moyen le plus simple pour référencer des dépendances Service Bus consiste à installer le [package Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) de Service Bus. Vous trouverez également l'assembly dans le kit de développement logiciel (SDK) d'Azure. Le téléchargement est disponible sur la [page de téléchargement du Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Créez la rubrique et les abonnements
Les opérations de gestion des entités de messagerie Service Bus (rubriques files d’attente et publication/abonnement) sont effectuées via la classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager). Des informations d’identification appropriées sont nécessaires pour créer une instance **NamespaceManager** pour un espace de noms particulier. Service Bus utilise un modèle de sécurité basé sur une [signature d’accès partagé (SAP)](service-bus-sas-overview.md). La classe [TokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) représente un fournisseur de jetons de sécurité dont les méthodes de fabrique intégrées renvoient des fournisseurs de jetons bien connus. Nous allons utiliser une méthode [CreateSharedAccessSignatureTokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) pour retenir les informations d’identification SAP. L’instance [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) est ensuite créée avec l’adresse de base de l’espace de noms Service Bus et du fournisseur de jetons.

La classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Le code ci-dessous montre comment l’instance **NamespaceManager** est créée et utilisée pour créer la rubrique **DataCollectionTopic**.

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);

namespaceManager.CreateTopic("DataCollectionTopic");
```

Notez qu’il existe des surcharges de la méthode [CreateTopic](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) qui vous permettent de définir les propriétés de la rubrique. Par exemple, vous pouvez définir la valeur par défaut de durée de vie « time-to-live » pour les messages envoyés à la rubrique. Ensuite, ajoutez les abonnements **Inventaire** et **Tableau de bord**.

```csharp
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Envoyez des messages à la rubrique
Pour des opérations d’exécution sur les entités Service Bus ; par exemple, pour l’envoi et la réception de messages, une application doit tout d’abord créer un objet [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#microsoft_servicebus_messaging_messagingfactory). Semblable à la classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager), l’instance **MessagingFactory** est créée à partir de l’adresse de base de l’espace de noms et du fournisseur de jetons.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Les messages envoyés aux rubriques Service Bus (et reçus de celles-ci) sont des instances de la classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Cette classe se compose d’un ensemble de propriétés standard (telles que [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) et [TimeToLive](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), un dictionnaire servant à conserver les propriétés propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps en transmettant un objet sérialisable (l’exemple suivant transmet un objet **SalesData** qui représente les données de ventes à partir du terminal de PDV), qui utilisera [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) pour sérialiser l’objet. Une autre possibilité consiste à fournir un objet [Stream](https://msdn.microsoft.com/library/system.io.stream.aspx).

```csharp
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Le moyen le plus simple pour envoyer des messages à la rubrique consiste à utiliser [CreateMessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) pour créer un objet [MessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesender) directement à partir de l’instance [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory).

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement
Semblable à l’utilisation des files d’attente, pour recevoir des messages à partir d’un abonnement, vous pouvez utiliser un objet [MessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagereceiver) que vous pouvez créer directement à partir de [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory) à l’aide de [CreateMessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_). Vous pouvez utiliser l’un des deux modes de réception (**ReceiveAndDelete** et **PeekLock**), comme indiqué dans [Création d’applications qui utilisent les files d’attente Service Bus](service-bus-create-queues.md).

Notez que lorsque vous créez un **MessageReceiver** pour les abonnements, le paramètre *entityPath* se présente comme suit : `topicPath/subscriptions/subscriptionName`. Par conséquent, pour créer un **MessageReceiver** pour l’abonnement **Inventaire** de la rubrique **DataCollectionTopic**, *entityPath* doit être défini sur `DataCollectionTopic/subscriptions/Inventory`. Le code apparaît comme suit :

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Filtres d’abonnement
Jusqu’ici, dans ce scénario, tous les messages envoyés à la rubrique sont rendus disponibles pour tous les abonnements inscrits. L'expression clé ici est « rendus disponibles ». Bien que les abonnements Service Bus voient tous les messages envoyés à la rubrique, vous pouvez uniquement copier un sous-ensemble de ces messages dans la file d’attente d’abonnement virtuelle. Pour ce faire, il faut utiliser des *filtres* d’abonnement. Lorsque vous créez un abonnement, vous pouvez fournir une expression de filtre sous la forme d’un prédicat de style SQL92 qui fonctionne avec les propriétés du message, à la fois les propriétés système (par exemple, [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label)) et les propriétés de l’application, telles que **StoreName** dans l’exemple précédent.

Pour que le scénario de vente au détail soit plus complet et pour illustrer ce propos, nous allons ajouter un deuxième magasin. Les données de vente de tous les terminaux de PDV des deux magasins doivent toujours être acheminées vers le système de gestion des stocks central. En revanche, le directeur de chaque magasin, qui utilise l’outil de tableau de bord, souhaite visualiser uniquement les données qui concernent les performances de son magasin. Pour répondre à cette difficulté, vous pouvez utiliser un filtre d’abonnement. Remarque : lorsque les terminaux de PDV publient des messages, la propriété de l’application **StoreName** est définie dans le message. Prenons deux magasins, par exemple **Redmond** et **Seattle**. Les terminaux de PDV du magasin de Redmond estampillent leurs messages de données de ventes avec un **StoreName** égal à **Redmond**. Les terminaux de PDV du magasin de Seattle, quant à eux, utilisent un **StoreName** égal à **Seattle**. Le responsable de magasin de Redmond souhaite accéder uniquement aux données recueillies à partir de ses terminaux de PDV. Le système se présente comme suit :

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Pour configurer ce routage, vous créez l’abonnement **Tableau de bord** de la façon suivante :

```csharp
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Avec ce [filtre d’abonnement](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter) en place, seuls les messages dont la propriété **StoreName** est définie sur **Redmond** sont copiés vers la file d’attente virtuelle de l’abonnement **Tableau de bord**. Le filtrage ne s’arrête pas aux abonnements, loin de là. Les applications peuvent présenter plusieurs règles de filtre par abonnement, ainsi que la possibilité de modifier les propriétés d’un message lorsqu’il passe dans la file d’attente virtuelle d’un abonnement.

## <a name="summary"></a>Résumé
Toutes les raisons d’utiliser la mise en file d’attente décrites dans [Création d’applications qui utilisent les files d’attente Service Bus](service-bus-create-queues.md) s’appliquent également aux rubriques, en particulier :

* Découplage temporel : les producteurs et les consommateurs de messages n'ont pas besoin d'être en ligne en même temps.
* Niveau de charge : les pics de charge sont apaisés par la rubrique qui permet aux applications consommatrices d’être configurées pour une charge moyenne au lieu de pics de charge.
* Équilibrage de la charge : semblable à une file d’attente, plusieurs consommateurs concurrents peuvent être à l’écoute sur un seul abonnement. Dans ce contexte, chaque message est transmis à un seul des consommateurs, équilibrant ainsi la charge.
* Couplage faible : vous pouvez faire évoluer le réseau de messagerie sans incidence sur les points de terminaison existants ; par exemple, ajoutez des abonnements ou modifiez des filtres dans une rubrique pour permettre l’ajout de nouveaux consommateurs.

## <a name="next-steps"></a>Étapes suivantes
Consultez [Création d’applications qui utilisent les files d’attente Service Bus](service-bus-create-queues.md) pour obtenir des informations sur l’utilisation des files d’attente dans le scénario de vente au détail de PDV.




<!--HONumber=Jan17_HO2-->


