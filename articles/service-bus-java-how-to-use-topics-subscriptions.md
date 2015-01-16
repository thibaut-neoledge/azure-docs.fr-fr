<properties urlDisplayName="Service Bus Topics" pageTitle="Utilisation des rubriques Service Bus (Java) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Java" description="Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure. Les exemples de code sont écrits pour les applications Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Utilisation des rubriques/abonnements Service Bus

Ce guide vous montre comment utiliser les rubriques et les
abonnements Service Bus. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure pour Java][]. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques
et d'abonnements**, **création de filtres d'abonnements**, **envoi
de messages à une rubrique**, **réception de messages en provenance d'un abonnement** et
**suppression de rubriques et d'abonnements**.

## Sommaire

-   [Présentation des rubriques et des abonnements Service Bus][]
-   [Création d'un espace de noms de service][]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][]
-   [Configuration de votre application pour l'utilisation de Service Bus][]
-   [ Création d'une rubrique][]
-   [ Création d'abonnements][]
-   [ Envoi de messages à une rubrique][]
-   [ Réception des messages d'un abonnement][]
-   [ Gestion des blocages d'application et des messages illisibles][]
-   [ Suppression de rubriques et d'abonnements][]
-   [Étapes suivantes][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Configuration de votre application pour l'utilisation de Service Bus

Ajoutez les instructions import suivantes au début du fichier Java :

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Ajoutez les bibliothèques Azure pour Java au chemin de votre build et incluez-le dans votre assembly de déploiement du projet.

## <a name="bkmk_HowToCreateTopic"> </a>Création d'une rubrique

Les opérations de gestion des rubriques Service Bus peuvent être effectuées via
la classe **ServiceBusContract**. Un objet **ServiceBusContract** est construit
avec une configuration appropriée qui encapsule les
autorisations de jetons pour le gérer, et la classe **ServiceBusContract**
est le point de communication unique avec Azure.

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer
et supprimer des rubriques. L'exemple suivant montre comment un objet **ServiceBusService**
peut être utilisé pour créer une rubrique nommée " TestTopic ", avec un espace de noms " HowToSample " :

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

Il existe des méthodes sur **TopicInfo** qui permettent de régler les propriétés de la rubrique
(par exemple : pour faire en sorte que la valeur par défaut de durée de vie (" time-to-live ") soit
appliquée aux messages envoyés à la rubrique). L'exemple suivant montre comment
créer une rubrique nommée " TestTopic " avec une taille maximale de 5 Go :

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Notez que vous pouvez utiliser la méthode **listTopics** sur les objets
**ServiceBusContract** pour vérifier s'il existe déjà une rubrique d'un certain nom
dans un espace de noms de service.

## <a name="bkmk_HowToCreateSubscrip"> </a>Création d'abonnements

Les abonnements de rubrique sont également créés à l'aide de la classe **ServiceBusService**
. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui
limite l'ensemble des messages transmis à la file d'attente virtuelle
de l'abonnement.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre
n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll**
est utilisé, tous les messages publiés dans la rubrique sont placés dans la
file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement " AllMessages "
qui est créé utilise le filtre par défaut **MatchAll**
.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés
à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements,
**SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL agissent
sur les propriétés des messages publiés dans la rubrique. Pour
plus de détails sur les expressions utilisables avec un filtre SQL,
examinez la syntaxe SqlFilter.SqlExpression.

Dans l'exemple ci-dessous, l'abonnement " HighMessages " est créé avec un filtre
**SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée
**MessageNumber** a une valeur supérieure à 3 :

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

De même, l'exemple suivant crée un abonnement nommé
" LowMessages " avec   
 un filtre SqlFilter qui sélectionne uniquement les messages dont la propriété MessageNumber
est inférieure   
 ou égale à 3 :

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


Lorsqu'un message est envoyé à " TestTopic ", il est toujours
remis aux destinataires abonnés à l'abonnement " AllMessages "
et est remis de manière sélective aux destinataires abonnés aux abonnements
" HighMessages " et " LowMessages " (en fonction du
contenu du message).

## <a name="bkmk_HowToSendMsgs"> </a>Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit obtenir un objet
**ServiceBusContract**. Le code ci-dessous montre comment envoyer un
message pour la rubrique " TestTopic " créée précédemment à notre
espace de noms du service " HowToSample " :

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Les messages envoyés aux rubriques Service Bus sont des instances de la classe
**BrokeredMessage**. Les objets **BrokeredMessage** disposent d'un ensemble de
méthodes standard (telles que **setLabel** et **TimeToLive**), un dictionnaire
servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps
de données d'application arbitraires. Une application peut définir le corps du
message en transmettant un objet sérialisable au constructeur de la classe
**BrokeredMessage** et le **DataContractSerializer** approprié
est alors utilisé pour sérialiser l'objet. Une autre possibilité consiste à fournir un
**java.io.InputStream**.

L'exemple suivant montre comment envoyer cinq messages de test à l'expéditeur
"TestTopic" **MessageSender** obtenu dans l'extrait de code précédent.
Notez que la valeur de la propriété **MessageNumber** de chaque message varie
au niveau de l'itération de la boucle (détermine l'abonnement
qui le reçoit) :

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête,
qui inclut les propriétés d'application standard et personnalisées, peut avoir
une taille maximale de 64 Mo). Il n'existe aucune limite pour le nombre de messages
dans une rubrique, mais il en existe une, en revanche, pour la taille totale des messages
dans une rubrique. Cette taille de rubrique est définie au moment de la création.
La limite maximale est de 5 Go.

## <a name="bkmk_HowToReceiveMsgs"> </a>Réception des messages d'un abonnement

Le principal moyen de recevoir les messages d'un abonnement est d'utiliser un objet
**ServiceBusContract**. Ces messages reçus peuvent fonctionner dans deux
modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération
unique : lorsque Service Bus reçoit une demande de lecture pour un
message, il marque le message comme étant consommé et le renvoie à
l'application. **ReceiveAndDelete** est le modèle le plus simple et mieux adapté
aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un
message en cas d'échec. Pour comprendre, imaginez un
scénario dans lequel le consommateur émet la demande de réception, puis
se bloque avant de le traiter. Comme Service Bus a marqué le
message comme étant consommé, lorsque l'application redémarre et recommence
à consommer des messages, elle manque le message
consommé avant l'incident.

En mode **PeekLock**, la réception devient une opération en deux étapes, qui autorise
une prise en charge des applications qui ne peuvent pas tolérer les messages
manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant
à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis
le renvoie à l'application. Dès que l'application a terminé
le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle
accomplit la deuxième étape du processus de réception en appelant la méthode **Delete**
sur le message reçu. Lorsque Service Bus obtient l'appel **Delete**, il
marque le message comme étant consommé et le supprime de la rubrique.

L'exemple ci-dessous montre comment les messages peuvent être reçus et
traités à l'aide du mode **PeekLock** (et non du mode par défaut). L'exemple
ci-dessous lance une boucle qui traite les messages de l'abonnement " HighMessages ", qui s'arrête lorsqu'il n'y a plus de messages (cette boucle peut aussi être configurée pour attendre de nouveaux messages).

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
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
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
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

## <a name="bkmk_HowToHandleAppCrash"> </a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération
à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une
application réceptrice ne parvient pas à traiter le message pour une raison quelconque,
elle appelle la méthode **unlockMessage** sur le message reçu.
(au lieu de la méthode **deleteMessage**). Cela amène Service Bus
à déverrouiller le message dans la rubrique et à le rendre à nouveau disponible
en réception, pour la même application consommatrice ou pour une autre
.

Il existe également un délai d'expiration pour un message verrouillé dans la
rubrique et si l'application ne parvient pas à traiter le message avant l'
expiration du délai d'attente du verrou (par exemple, si l'application se bloque), Service
Bus déverrouille le message automatiquement et le rend à nouveau disponible
en réception.

Dans le cas où l'application se bloque après le traitement du message
, mais avant l'envoi de la demande **deleteMessage** , le message est à nouveau
remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé
**Au moins une fois**, chaque message est
traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être
remis une nouvelle fois. Si le scénario ne peut pas tolérer un traitement en double,
les développeurs d'applications doivent ajouter une logique supplémentaire à leur
application pour traiter la remise de messages en double. Cette opération est souvent réalisée
à l'aide de la méthode **getMessageId** du message, qui reste
constante pendant les tentatives de remise.

## <a name="bkmk_HowToDeleteTopics"> </a>Suppression de rubriques et d'abonnements

Le principal moyen de supprimer des rubriques et des abonnements est d'utiliser un objet
**ServiceBusContract**. La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits
au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez la rubrique MSDN
[Files d'attente, rubriques et abonnements Service Bus][] pour plus d'informations.

  [Kit de développement logiciel (SDK) Azure pour Java]: http://www.windowsazure.com/fr-fr/develop/java/
  [Présentation des rubriques et des abonnements Service Bus]: #what-are-service-bus-topics
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Configuration de votre application pour l'utilisation de Service Bus]: #bkmk_ConfigYourApp
  [ Création d'une rubrique]: #bkmk_HowToCreateTopic
  [ Création d'abonnements]: #bkmk_HowToCreateSubscrip
  [ Envoi de messages à une rubrique]: #bkmk_HowToSendMsgs
  [ Réception des messages d'un abonnement]: #bkmk_HowToReceiveMsgs
  [ Gestion des blocages d'application et des messages illisibles]: #bkmk_HowToHandleAppCrash
  [ Suppression de rubriques et d'abonnements]: #bkmk_HowToDeleteTopics
  [Étapes suivantes]: #bkmk_NextSteps
  [Diagramme des rubriques Service Bus]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portail de gestion Azure]: http://manage.windowsazure.com/
  [Capture d'écran du nœud Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Création d'un espace de noms ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Capture d'écran Liste d'espaces de noms]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Capture d'écran du volet Propriétés]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Capture d'écran Clé par défaut]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Files d'attente, rubriques et abonnements Service Bus]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
