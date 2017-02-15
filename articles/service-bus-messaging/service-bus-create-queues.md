---
title: "Écriture d’applications qui utilisent les files d’attente Service Bus | Microsoft Docs"
description: "Comment écrire une application simple basée sur la file d&quot;attente qui utilise Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 754d91b3-1426-405e-84b4-fd36d65b114a
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2350c3e222277b6d8e837472f55a7b79346d3d21


---
# <a name="create-applications-that-use-service-bus-queues"></a>Création d'applications qui utilisent les files d'attente Service Bus
Cette rubrique décrit les files d’attente Service Bus et montre comment écrire une application simple basée sur les files d’attente et qui utilise Service Bus.

Imaginez. Vous voilà plongé au cœur de la vente au détail. Les données de vente recueillies à partir de terminaux de point de vente (PDV) doivent être acheminées vers un système de gestion des stocks. Celui-ci utilise ces données pour déterminer le moment où il est nécessaire de renouveler les stocks. Cette solution utilise les messages Service Bus pour communiquer entre les terminaux et le système de gestion des stocks, comme illustré dans la figure suivante :

![Image 1 Files d’attente Service Bus](./media/service-bus-create-queues/IC657161.gif)

Chaque terminal de PDV consigne ses données de vente en envoyant des messages à la **DataCollectionQueue**. Ces messages restent dans cette file d'attente jusqu'à ce qu'ils soient récupérés par le système de gestion des stocks. Ce modèle est souvent appelé *messagerie asynchrone*, car le terminal de PDV n’a pas à attendre une réponse à partir du système de gestion des stocks pour poursuivre le traitement.

## <a name="why-queuing"></a>Avantages de la file d'attente
Avant d'examiner le code nécessaire à la configuration de cette application, considérez les avantages de l'utilisation d'une file d'attente dans ce scénario par rapport à la communication directe (synchrone) entre les terminaux de PDV et le système de gestion des stocks.

### <a name="temporal-decoupling"></a>Découplage temporel
Avec le modèle de messagerie asynchrone, les producteurs et les consommateurs n'ont pas besoin d'être en ligne en même temps. L'infrastructure de la messagerie stocke les messages de façon fiable jusqu'à ce que le récepteur soit prêt à les recevoir. Ceci permet aux composants de l’application distribuée d’être déconnectés, soit volontairement, par exemple pour des raisons de maintenance, soit en raison de l’échec d’un composant, sans conséquences sur le système dans sa globalité. De plus, l'application qui utilise les messages n'a besoin d'être en ligne que quelques fois par jour. Par exemple, dans ce scénario de vente au détail, le système de gestion des stocks peut très bien être en ligne uniquement en fin de journée.

### <a name="load-leveling"></a>Niveau de charge
Dans de nombreuses applications, la charge système varie dans le temps, alors que le temps de traitement nécessaire à chaque unité de travail est normalement constant. L'ajout d'une file d'attente entre les producteurs et les consommateurs des messages fait que l'application de destination (le rôle de travail) n'a besoin d'être configurée que pour une charge de travail moyenne, plutôt que pour une charge de travail maximale. La file d'attente s'allonge et se raccourcit en fonction de la charge entrante. Ceci permet de faire des économies concernant les infrastructures nécessaires pour faire face à la charge de travail de l’application.

![Image 2 Files d’attente Service Bus](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Équilibrage de la charge
À mesure que la charge augmente, d'autres processus de travail peuvent être ajoutés pour lire les éléments de la file d'attente de travail. Chaque message est traité par un seul des processus de travail. De plus, cet équilibrage de la charge basé sur l'extraction permet d'optimiser l'utilisation des ordinateurs de travail, même si ceux-ci diffèrent en termes de puissance de traitement, car ils demandent les messages au maximum de leur capacité. Ce modèle est souvent appelé modèle consommateur concurrent.

![Image 3 Files d’attente Service Bus](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Couplage faible
L'utilisation de files d'attente de message comme intermédiaire entre les producteurs et les consommateurs de messages fournit un couplage souple et intrinsèque entre les composants. Producteurs et consommateurs étant indépendants les uns des autres, il est possible de mettre à niveau un consommateur sans que cela affecte le producteur. En outre, la topologie de messagerie peut évoluer sans affecter les points de terminaison existants. Nous y reviendrons plus en détail lorsque nous aborderons la publication/l'abonnement.

## <a name="show-me-the-code"></a>Afficher le code
La section suivante montre comment utiliser Service Bus pour créer cette application.

### <a name="sign-up-for-an-azure-account"></a>Créer un compte Azure
Vous aurez besoin d'un compte Azure pour commencer à travailler avec Service Bus. Si vous n’en avez pas, vous pouvez vous inscrire [ici](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF) pour obtenir un compte gratuit.

### <a name="create-a-namespace"></a>Créer un espace de noms
Une fois que vous avez un abonnement, vous pouvez [créer un espace de noms](service-bus-create-namespace-portal.md). Un espace de noms est un conteneur d’étendue pour un jeu d’entités Service Bus. Donnez un nom unique à votre nouvel espace de noms sur tous les comptes Service Bus. 

### <a name="install-the-nuget-package"></a>Installez le package NuGet
Pour utiliser l’espace de noms Service Bus, une application doit référencer l’assembly Service Bus, et plus précisément le fichier Microsoft.ServiceBus.dll. Vous pouvez retrouver cet assembly dans le kit de développement logiciel (SDK) Microsoft Azure. Le téléchargement est disponible sur la [page de téléchargement du kit de développement (SDK) de Microsoft Azure](https://azure.microsoft.com/downloads/). Toutefois, le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple de se procurer l’API Service Bus et de configurer votre application avec toutes les dépendances Service Bus disponibles.

### <a name="create-the-queue"></a>Créer la file d’attente
Les opérations de gestion des entités de messagerie Service Bus (rubriques files d’attente et publication/abonnement) sont effectuées via la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Service Bus utilise un modèle de sécurité basé sur une [signature d’accès partagé (SAP)](service-bus-sas-overview.md). La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jetons de sécurité dont les méthodes de fabrique intégrées renvoient des fournisseurs de jetons bien connus. Nous allons utiliser une méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour retenir les informations d’identification SAP. L’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est ensuite créée avec l’adresse de base de l’espace de noms Service Bus et du fournisseur de jetons.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Le code ci-dessous montre comment l’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est créée et utilisée pour créer la file d’attente **DataCollectionQueue**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Notez qu’il existe des surcharges de la méthode [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) qui activent les propriétés de la file d’attente à paramétrer. Par exemple, vous pouvez définir la valeur par défaut de durée de vie « time-to-live » pour les messages envoyés à la file d'attente.

### <a name="send-messages-to-the-queue"></a>Envoyez des messages à la file d'attente
Pour des opérations d’exécution sur les entités Service Bus ; par exemple, pour l’envoi et la réception de messages, une application doit tout d’abord créer un objet [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Semblable à la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), l’instance [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) est créée à partir de l’adresse de base de l’espace de noms et du fournisseur de jetons.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Les messages envoyés aux files d’attente Service Bus (et reçus de celles-ci) sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Cette classe se compose d’un ensemble de propriétés standard (telles que [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire servant à conserver les propriétés propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps en transmettant un objet sérialisable (l’exemple suivant transmet un objet **SalesData** qui représente les données de ventes à partir du terminal de PDV), qui utilisera [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) pour sérialiser l’objet. Une autre possibilité consiste à fournir un objet [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

Le moyen le plus simple pour envoyer des messages à une file d’attente donnée, dans notre cas **DataCollectionQueue**, consiste à utiliser [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) pour créer un objet [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directement à partir de l’instance [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Réception de messages à partir de la file d'attente
Pour recevoir des messages de la file d’attente, vous pouvez utiliser un objet [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que vous créez directement à partir de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) à l’aide de [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Les destinataires de message peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**. Le [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) est défini lorsque le destinataire du message est créé, en tant que paramètre de l’appel [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).

En mode **ReceiveAndDelete**, l’opération est exécutée une seule fois, en d’autres termes, lorsque Service Bus reçoit la demande, il marque ce message comme étant consommé et le renvoie à l’application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d’un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Service Bus ayant marqué le message comme étant consommé, lorsque l'application redémarre et recommence à traiter des messages, elle ignore le message consommé avant l'incident.

En mode **PeekLock**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit la demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) pour le message reçu. Lorsque Service Bus obtient l’appel [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), il marque le message comme étant consommé.

Deux autres issues sont possibles. Tout d’abord, si une application est dans l’impossibilité de traiter un message pour une raison quelconque, elle peut appeler la méthode [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) pour le message reçu (au lieu de la méthode [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Cela permet à Service Bus de déverrouiller le message et le met à disposition pour être à nouveau reçu, soit par le même consommateur, soit par un consommateur concurrent. Ensuite, il faut savoir qu’un message verrouillé dans une file d’attente est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, en cas d’incident), Service Bus déverrouillera le message automatiquement et le rendra à nouveau disponible en réception (essentiellement en effectuant une opération [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) par défaut).

Notez que si l’application subit un incident après le traitement du message, mais avant l’envoi de la demande [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), le message est à nouveau remis à l’application lorsqu’elle redémarre. Cette opération est souvent appelée *Au moins une fois*. En d'autres termes, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer un traitement dupliqué, une logique supplémentaire est nécessaire dans l'application pour détecter les doublons. Cela peut être effectué en fonction de la propriété [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message. La valeur de cette propriété ne change pas entre chaque tentative de remise. Cette opération est connue sous le nom de traitement *seulement une fois*.

Le code présenté ici reçoit et traite un message en utilisant le mode **PeekLock**, qui est la valeur par défaut si aucune valeur [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) n’est fournie explicitement.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### <a name="use-the-queue-client"></a>Utilisez le client de la file d'attente
Les exemples cités plus haut dans cette section ont directement créé les objets [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) et [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) à partir de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) pour envoyer et recevoir des messages de la file d’attente, respectivement. Une autre approche consiste à utiliser la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) qui prend en charge les opérations d’envoi et de réception en plus des fonctionnalités plus avancées, telles que les sessions.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);

BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les fondamentaux des files d’attente, consultez [Création d’applications qui utilisent des rubriques et des abonnements Service Bus](service-bus-create-topics-subscriptions.md) pour poursuivre cette discussion à l’aide des fonctions de publication/d’abonnement des rubriques et abonnements Service Bus.




<!--HONumber=Nov16_HO3-->


