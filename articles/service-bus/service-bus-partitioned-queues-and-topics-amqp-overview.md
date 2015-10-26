<properties 
	pageTitle="Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente Service Bus partitionnées | Microsoft Azure" 
	description="Apprendre comment utiliser Advanced Message Queuing Protocol (AMQP) 1.0 dans les rubriques et files d’attente partitionnées Azure Service Bus." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="hillaryc" 
	editor="hillaryc"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="hillaryc"/>



# Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente Service Bus partitionnées 

Azure Service Bus prend désormais en charge le protocole Advanced Message Queuing Protocol (**AMQP**) 1.0 pour les **rubriques et files d’attente partitionnées** Azure Service Bus.

**AMQP** est un protocole de mise en file d’attente de messages ouvert, qui permet de développer des applications inter plateformes utilisant des langages de programmation différents. Vous trouverez d’autres informations sur la prise en charge générale de Service Bus AMQP dans la rubrique [Prise en charge d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md).

Les **files d’attente et rubriques partitionnées**, également connues sous le nom d’entités partitionnées, offrent une disponibilité, une fiabilité et un débit supérieurs à ceux des files d’attente et rubriques classiques. Vous trouverez d’autres informations sur les entités partitionnées en consultant la section [Partitionnement des entités de messagerie](https://msdn.microsoft.com/library/azure/dn520246.aspx).

L’ajout d’AMQP 1.0 en tant que protocole de communication avec des files d’attente et des rubriques partitionnées permet aux clients de créer des applications interfonctionnelles offrant la disponibilité, la fiabilité et le débit supérieurs des entités Service Bus partitionnées.

### Utilisation d’AMQP avec les files d’attente partitionnées

Les files d’attente sont utiles pour les scénarios qui requièrent le découplage temporel, le nivellement et l’équilibrage des charges et des couplages faibles. Dans une file d’attente, les éditeurs envoient des messages à la file d’attente et les consommateurs reçoivent des messages de la file d’attente. Un message ne peut être reçu qu’une fois. Un exemple classique est un système d’inventaire dans lequel les terminaux de points de vente publient les données dans une file d’attente au lieu de les envoyer directement au système de gestion de stock. Le système de gestion de stock consomme alors ces données à tout moment pour assurer le réapprovisionnement du stock. Cela comporte plusieurs avantages. En effet, le système de gestion de stock n’a pas à être en constamment en ligne. Vous trouverez des informations supplémentaires sur les files d’attente de Service Bus ici : [Créations d’applications utilisant les files d’attente Service Bus](https://msdn.microsoft.com/library/azure/hh689723.aspx)

Une file d’attente partitionnée peut augmenter la disponibilité, la fiabilité et le débit des applications, car elles sont partitionnées entre différents répartiteurs de messages et banques de messagerie.

#### Création de files d’attente partitionnées

Une file d’attente partitionnée peut être créée via le portail Azure et le kit de développement logiciel Service Bus. Pour créer une file d’attente partitionnée, la propriété EnablePartitioning doit être définie sur true dans l’instance QueueDescription. L’extrait de code ci-dessous montre comment en créer une à l’aide du kit de développement logiciel Service Bus.
 
	// Create partitioned queue
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var queueDescription = new QueueDescription("myQueue");
	queueDescription.EnablePartitioning = true;
	nm.CreateQueue(queueDescription);

#### Envoi et réception de messages à l’aide d’AMQP

L’envoi de messages à une file d’attente partitionnée et la réception de messages de cette file d’attente partitionnée à l’aide du protocole AMQP s’effectue en définissant le type de transport pour TransportType.Amqp, comme indiqué dans l’extrait de code ci-dessous.

	// Sending and receiving messages to and from a Queue
	var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
	myConnectionStringBuilder.TransportType = TransportType.Amqp;
	string amqpConnectionString = myConnectionStringBuilder.ToString();
	var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

	BrokeredMessage message = new BrokeredMessage("Hello AMQP");
	Console.WriteLine("Sending message {0}...", message.MessageId);
	queueClient.Send(message);

	var receivedMessage = queueClient.Receive();
	Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
	receivedMessage.Complete();


### Utilisation d’AMQP avec des rubriques partitionnées

Comme les files d’attente, les rubriques sont utiles pour les scénarios qui requièrent le découplage temporel, le nivellement et l’équilibrage des charges et des couplages faibles. Contrairement aux files d’attente, les rubriques peuvent acheminer une copie du même message à plusieurs abonnés. Dans une rubrique, les serveurs de publication envoient des messages à la rubrique et les consommateurs reçoivent des messages à partir d’abonnements. Dans l’exemple d’inventaire de terminaux de points de vente, les terminaux publieraient les données dans la rubrique. Le système de gestion du stock reçoit alors les messages d’un abonnement. En outre, un système de surveillance peut recevoir le même message à partir d’un autre abonnement. Vous trouverez des informations supplémentaires sur les files d’attente de Service Bus ici : [Créations d’applications utilisant les files d’attente et les abonnements Service Bus](https://msdn.microsoft.com/library/azure/hh699844.aspx)

Une rubrique partitionnée peut augmenter la disponibilité, la fiabilité et le débit des applications, car elles sont réparties sur plusieurs répartiteurs de messages et banques de messagerie.

#### Création de rubriques partitionnées

Une rubrique partitionnée peut être créée via le portail Azure et le kit de développement logiciel Service Bus. Pour créer une rubrique partitionnée, la propriété EnablePartitioning doit être définie sur true dans l’instance TopicDescription. L’extrait de code ci-dessous montre comment en créer une à l’aide du kit de développement logiciel Service Bus.
	
	// Create partitioned topic
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var topicDescription = new TopicDescription("myTopic");
	topicDescription.EnablePartitioning = true;
	nm.CreateTopic(topicDescription);

	var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
	nm.CreateSubscription(subscriptionDescription);

#### Envoi et réception de messages à l’aide d’AMQP

L’envoi de messages à une file d’attente partitionnée et la réception de messages de cette file d’attente partitionnée à l’aide du protocole AMQP s’effectue en définissant le type de transport sur TransportType.Amqp, comme indiqué dans l’extrait de code ci-dessous.

	// Sending and receiving messages to a topic and from a subscription
	var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
	myConnectionStringBuilder.TransportType = TransportType.Amqp;
	string amqpConnectionString = myConnectionStringBuilder.ToString();
	
	var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
	BrokeredMessage message = new BrokeredMessage("Hello AMQP");
	Console.WriteLine("Sending message {0}...", message.MessageId);
	topicClient.Send(message);
	
	var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
	var receivedMessage = subcriptionClient.Receive();
	Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
	receivedMessage.Complete();


## Références

*    [Partitionnement des entités de messagerie](https://msdn.microsoft.com/library/azure/dn520246.aspx)
*    [OASIS Advanced Message Queuing Protocol (AMQP) Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Prise en charge d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md)
*    [AMQP Service Bus : guide du développeur]("https://msdn.microsoft.com/library/azure/jj841071.aspx")
*    [Utilisation de l’API Java Message Service (JMS) avec Service Bus et AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Utilisation d’AMQP 1.0 avec l’API .NET Service Bus](service-bus-dotnet-advanced-message-queuing.md)

<!---HONumber=Oct15_HO3-->