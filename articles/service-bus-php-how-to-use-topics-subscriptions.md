<properties urlDisplayName="Service Bus Topics" pageTitle="Utilisation des rubriques Service Bus (PHP) - Azure" metaKeywords="" description="Découvrez comment utiliser les rubriques Service Bus avec PHP dans Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />


# Utilisation des rubriques/abonnements Service Bus

Ce guide vous montre comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP][download-sdk]. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements**, **création de filtres d'abonnement**, **envoi de messages à une rubrique**, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**.

## Sommaire

-   [Présentation des rubriques et des abonnements Service Bus](#what-are-service-bus-topics)
-   [Création d'un espace de noms de service](#create-a-service-namespace)
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms](#obtain-default-credentials)
- 	[Création d'une application PHP](#CreateApplication)
-	[Obtention des bibliothèques clientes Azure](#GetClientLibrary)
-   [Configuration de votre application pour l'utilisation de Service Bus](#ConfigureApp)
-   [ Création d'une rubrique](#CreateTopic)
-   [ Création d'un abonnement](#CreateSubscription)
-   [ Envoi de messages à une rubrique](#SendMessage)
-   [ Réception des messages d'un abonnement](#ReceiveMessages)
-   [ Gestion des blocages d'application et des messages illisibles](#HandleCrashes)
-   [ Suppression de rubriques et d'abonnements](#DeleteTopicsAndSubscriptions)
-   [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a id="CreateApplication"></a>Création d'une application PHP

La référence de classes du [Kit de développement logiciel (SDK) Azure pour PHP][download-sdk] constitue la seule exigence pour créer une application PHP qui accède au service Blob Azure dans votre code. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

> [WACOM.NOTE]
> L'extension OpenSSL <a href="http://php.net/openssl">doit également être</a> installée et activée dans votre installation PHP.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP localement, ou dans du code qui s'exécute dans un rôle web, un rôle de travail ou un site web Azure.

##<a id="GetClientLibrary"></a>Obtention des bibliothèques clientes Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser les API de rubrique Azure Service Bus, vous devez procéder comme suit :

1. Référencer le fichier de chargeur automatique à l'aide de l'instruction [require_once][require-once] et
2. référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServicesBuilder**.

	> [WACOM.NOTE]
	> Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez référencer le fichier de chargeur automatique <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Dans les exemples ci-dessous, l'instruction require_once s'affichera toujours, mais seules les classes nécessaires aux besoins de l'exemple à exécuter sont référencées.

##<a id="ConnectionString"></a>Configuration d'une connexion Azure Service Bus

Pour instancier un client Azure Service Bus, vous devez disposer au préalable d'une chaîne de connexion valide au format suivant :

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Où le point de terminaison est généralement au format https://[votre_espace_de_noms].servicebus.windows.net.

Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

* lui passer directement la chaîne de connexion ; ou
* utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
	* par défaut une source externe est prise en charge : variables d'environnement ;
	* de nouvelles sources peuvent être ajoutées via une extension de la classe **ConnectionStringSource**.

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	
	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

##<a id="CreateTopic"></a> Création d'une rubrique

Les opérations de gestion des rubriques Service Bus peuvent être effectuées via
**la classe ServiceBusRestProxy**. Un objet **ServiceBusRestProxy** est
construit par le biais de la méthode de fabrique **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations de jeton pour le gérer.

L'exemple ci-dessous montre comment instancier un **ServiceBusRestProxy** et appeler **ServiceBusRestProxy->createTopic** pour créer une rubrique nommée mytopic dans un espace de noms MySBNamespace :

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\TopicInfo;
	
	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Create topic.
		$topicInfo = new TopicInfo("mytopic");
		$serviceBusRestProxy->createTopic($topicInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	> [WACOM.NOTE]
	> You can use the <b>listTopics</b> method on <b>Des objets ServiceBusRestProxy</b> pour vérifier si une rubrique portant un nom spécifié existe déjà dans un espace de noms de service.

##<a id="CreateSubscription"></a> Création d'un abonnement

Les abonnements de rubrique sont également créés à l'aide de la méthode **ServiceBusRestProxy->createSubscription**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est utilisé par défaut si aucun filtre n'est spécifié pendant la création d'un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement " mysubscription " créé utilise le filtre par défaut **MatchAll**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		// Create subscription.
		$subscriptionInfo = new SubscriptionInfo("mysubscription");
		$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique. Parmi les types de filtre pris en charge par les abonnements, **SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d'informations sur SqlFilters, consultez la page [Propriété SqlExpression][sqlfilter].

	> [WACOM.NOTE]
	> Chaque règle d'un abonnement traite les messages entrants de façon indépendante, ajoutant leurs messages de résultat à l'abonnement. En outre, chaque nouvel abonnement dispose d'une <b>Règle</b> par défaut avec un filtre qui ajoute tous les messages de la rubrique à l'abonnement. Pour recevoir uniquement les messages correspondant à votre filtre, vous devez supprimer la règle par défaut. Vous pouvez supprimer la règle par défaut à l'aide de la méthode <b>ServiceBusRestProxy->deleteRule</b>.

Dans l'exemple ci-dessous, l'abonnement " HighMessages " est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **MessageNumber** a une valeur supérieure à 3 (consultez la section [ Envoi de messages à une rubrique](#SendMessage) pour plus d'informations sur l'ajout de propriétés personnalisées aux messages) :

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Note that the code above requires the use of an additional namespace: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Similarly, the following example creates a subscription named "LowMessages" with a SqlFilter that only selects messages that have a MessageNumber property less than or equal to 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

À présent, dès lors qu'un message est envoyé vers la rubrique mytopic, il est toujours remis aux destinataires abonnés à l'abonnement mysubscription et est remis de manière sélective aux destinataires abonnés aux abonnements " HighMessages " et " LowMessages " (en fonction du contenu du message).

##<a id="SendMessage"></a> Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application appelle la méthode **ServiceBusRestProxy->sendTopicMessage**. Le code ci-dessous montre comment envoyer un message à la rubrique mytopic créée précédemment dans
l'espace de noms de service MySBNamespace.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Les messages envoyés aux rubriques Service Bus sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés et de méthodes standard (telles que **getLabel**, **getTimeToLive**, **setLabel** et **setTimeToLive**), ainsi que des propriétés pouvant être utilisées pour conserver les propriétés personnalisées propres à une application. L'exemple suivant montre comment envoyer cinq messages de test à la rubrique mytopic créée précédemment. La méthode **setProperty** est utilisée pour ajouter une propriété personnalisée (MessageNumber) à chaque message. Notez que la valeur de la propriété MessageNumber varie pour chaque message (cela peut être utilisé pour déterminer les abonnements qui le reçoivent, comme indiqué dans la section [ Création d'un abonnement](#CreateSubscription) ci-dessus) :

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. La taille maximale de la file d'attente est de 5 Go.

##<a id="ReceiveMessages"></a> Réception des messages d'un abonnement

Le moyen principal de recevoir des messages d'un abonnement consiste à utiliser une méthode **ServiceBusRestProxy->receiveSubscriptionMessage**. Ces messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : lorsque Service Bus reçoit une demande de lecture pour un message figurant dans un abonnement, il marque le message comme étant consommé et le renvoie à l'application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock**, la réception d'un message devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en transmettant le message reçu à **ServiceBusRestProxy->deleteMessage**. Lorsque Service Bus obtient l'appel **deleteMessage**, il marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités avec le mode **PeekLock** (différent du mode par défaut). 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set receive mode to PeekLock (default is ReceiveAndDelete)
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
	
		// Get message.
		$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
																	"mysubscription", 
																	$options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Deleting message...<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a> Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour le message reçu (au lieu de la méthode **deleteMessage**). Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application se bloque après le traitement du message, mais avant l'envoi de la demande **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Ceci est souvent obtenu grâce à la propriété **getMessageId** du message, qui reste constante pendant les tentatives de remise.

##<a id="DeleteTopicsAndSubscriptions"></a>Suppression de rubriques et d'abonnements

Pour supprimer une rubrique ou un abonnement, utilisez la méthode **ServiceBusRestProxy->deleteTopic** ou **ServiceBusRestProxy->deleteSubscripton** respectivement. Notez que la suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique.

L'exemple suivant montre comment supprimer une rubrique (mytopic) et ses abonnements inscrits.

    require_once 'vendor\autoload.php';

	use WindowsAzure\ServiceBus\ServiceBusService;
	use WindowsAzure\ServiceBus\ServiceBusSettings;
	use WindowsAzure\Common\ServiceException;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Delete topic.
		$serviceBusRestProxy->deleteTopic("mytopic");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

La méthode **deleteSubscription** permet de supprimer un abonnement de façon indépendante :

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

##<a id="NextSteps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez la rubrique MSDN [Files d'attente, rubriques et abonnements Service Bus][] pour plus d'informations.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Présentation des rubriques et des abonnements Service Bus]: #bkmk_WhatAreSvcBusTopics
[Création d'un espace de noms de service]: #bkmk_CreateSvcNamespace
[Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #bkmk_ObtainDefaultMngmntCredentials
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
[Capture d'écran Créer un espace de noms]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Capture d'écran Liste d'espaces de noms]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Capture d'écran du volet Propriétés]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Capture d'écran Clé par défaut]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
[Capture d'écran Available Namespaces]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once
