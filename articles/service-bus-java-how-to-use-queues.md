<properties urlDisplayName="Service Bus Queues" pageTitle="Utilisation des files d'attente Service Bus (Java)" metaKeywords="files d'attente Azure Service Bus, files d'attente Azure, messagerie Azure, files d'attente Azure Java" description="Learn how to use Service Bus queues in Azure. Code samples written in Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Utilisation des files d'attente Service Bus

Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure pour Java][]. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente**, **envoi et réception de messages** et **suppression de files d'attente**.

## Sommaire

-   [Présentation des files d'attente Service Bus][]
-   [Création d'un espace de noms de service][]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][]
-   [Configuration de votre application pour l'utilisation de Service Bus][]
-   [ Création d'un fournisseur de jeton de sécurité][]
-   [ Création d'une file d'attente][How to: Create a Security Token Provider]
-   [ Envoi de messages à une file d'attente][]
-   [ Réception des messages d'une file d'attente][]
-   [ Gestion des blocages d'application et des messages illisibles][]
-   [Étapes suivantes][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a>Configuration de votre application pour l'utilisation de Service Bus

Ajoutez les instructions import suivantes au début du fichier Java :

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## <a name="bkmk_HowToCreateQueue"> </a>Création d'une file d'attente

Vous pouvez effectuer des opérations de gestion pour les files d'attente Service Bus via la classe **ServiceBusContract**. Un objet **ServiceBusContract** est construit avec une configuration appropriée qui encapsule les autorisations de jetons pour le gérer, et la classe **ServiceBusContract** est le point de communication unique avec Azure.

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer et supprimer des files d'attente. L'exemple suivant montre comment un objet **ServiceBusService** peut servir à créer une file d'attente nommée " TestQueue " avec un espace de noms nommé " HowToSample " :

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {     
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Il existe des méthodes sur QueueInfo qui permettent de régler les propriétés de la file d'attente (par exemple : pour faire en sorte que la valeur par défaut de durée de vie " time-to-live " soit appliquée aux messages envoyés à la file d'attente). L'exemple suivant montre comment créer une file d'attente nommée " TestQueue " avec une taille maximale de 5 Go :

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Notez que vous pouvez utiliser la méthode **listQueues** sur les objets **ServiceBusContract** pour vérifier s'il existe déjà une file d'attente avec un nom défini dans un espace de noms de service.

## <a name="bkmk_HowToSendMsgs"> </a>Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application obtient un objet **ServiceBusContract**. Le code ci-dessous montre comment créer un message pour la file d'attente " TestQueue " créée précédemment à l'aide de l'espace de noms de service " HowToSample " :

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e) 
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Les messages à destination (et en provenance) des files d'attente Service Bus sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de méthodes standard (telles que **getLabel**, **getTimeToLive**, **setLabel** et **setTimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l'objet **BrokeredMessage** ; le sérialiseur approprié est alors utilisé pour sérialiser l'objet. Une autre possibilité consiste à fournir un **java.IO.InputStream**.

L'exemple suivant montre comment envoyer cinq messages de test au 
MessageSender**" TestQueue "** obtenu dans l'extrait de code ci-dessus :

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## <a name="bkmk_HowToReceiveMsgs"> </a>Réception des messages d'une file d'attente

Le moyen principal pour recevoir des messages d'une file d'attente consiste à utiliser un objet **ServiceBusContract**. Ces messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.  Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : quand Service Bus reçoit une demande de lecture pour un message figurant dans une file d'attente, il marque le message comme étant consommé et le renvoie à l'application. Le mode **ReceiveAndDelete** (mode par défaut) est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant **Delete** pour le message reçu. Lorsque Service Bus obtient l'appel **Delete**, il marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide du mode **PeekLock** (et non du mode par défaut). Dans cet exemple, une boucle infinie est créée et les messages sont traités à mesure qu'ils parviennent à la file d'attente " TestQueue " :

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
	         ReceiveQueueMessageResult resultQM = 
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the queue message.
			    System.out.print("From queue: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " + 
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
		    }  
		    else  
		    {        
		        System.out.println("Finishing up - no more messages.");        
		        break; 
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	} 	

## <a name="bkmk_HowToHandleAppCrashes"> </a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour le message reçu (au lieu de la méthode **deleteMessage**). Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application se bloque après le traitement du message, mais avant l'envoi de la demande **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Ceci est souvent obtenu grâce à la propriété **getMessageId** du message, qui reste constante pendant les tentatives de remise.

## <a name="bkmk_NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez la rubrique MSDN
[Files d'attente, rubriques et abonnements][] pour plus d'informations.

  [Kit de développement logiciel (SDK) Azure pour Java]: http://azure.microsoft.com/fr-fr/develop/java/
  [Présentation des files d'attente Service Bus]: #what-are-service-bus-queues
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Configuration de votre application pour l'utilisation de Service Bus]: #bkmk_ConfigApp
  [ Création d'un fournisseur de jeton de sécurité]: #bkmk_HowToCreateQueue
  [ Envoi de messages à une file d'attente]: #bkmk_HowToSendMsgs
  [ Réception des messages d'une file d'attente]: #bkmk_HowToReceiveMsgs
  [ Gestion des blocages d'application et des messages illisibles]: #bkmk_HowToHandleAppCrashes
  [Étapes suivantes]: #bkmk_NextSteps
  [Portail de gestion Azure]: http://manage.windowsazure.com/
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
