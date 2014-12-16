<properties urlDisplayName="Service Bus Queues" pageTitle="Utilisation des files d'attente Service Bus (PHP) - Azure" metaKeywords="files d'attente Azure Service Bus, files d'attente Azure, messagerie Azure, files d'attente Azure PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />

# Utilisation des files d'attente Service Bus

Ce guide montre comment utiliser les files d'attente Service Bus avec PHP. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP][download-sdk]. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente**, **envoi et réception de messages** et **suppression de files d'attente**.

## Sommaire

-   [Présentation des files d'attente Service Bus](#what-are-service-bus-queues)
-   [Création d'un espace de noms de service](#create-a-service-namespace)
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms](#obtain-default-credentials)
- 	[Création d'une application PHP](#CreateApplication)
-	[Obtention des bibliothèques clientes Azure](#GetClientLibrary)
-   [Configuration de votre application pour l'utilisation de Service Bus](#ConfigureApp)
-   [ Création d'une file d'attente](#CreateQueue)
-   [ Envoi de messages à une file d'attente](#SendMessages)
-   [ Réception des messages d'une file d'attente](#ReceiveMessages)
-   [ Gestion des blocages d'application et des messages illisibles](#HandleCrashes)
-   [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>Création d'une application PHP

La référence de classes du [Kit de développement logiciel (SDK) Azure pour PHP][download-sdk] constitue la seule exigence pour créer une application PHP qui accède au service Blob Azure dans votre code. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

> [WACOM.NOTE]
> L'extension OpenSSL <a href="http://php.net/openssl">doit également être</a> installée et activée dans votre installation PHP.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP localement, ou dans du code qui s'exécute dans un rôle web, un rôle de travail ou un site web Azure.

##<a id="GetClientLibrary"></a>Obtention des bibliothèques clientes Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser des API de file d'attente Azure Service Bus, vous devez procéder comme suit :

1. Référencer le fichier de chargeur automatique à l'aide de l'instruction [require_once][require_once] et
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


##<a id="CreateQueue"></a> Création d'une file d'attente

Vous pouvez effectuer des opérations de gestion pour les files d'attente Service Bus via la classe **ServiceBusRestProxy**. Un objet **ServiceBusRestProxy** est construit via la méthode de fabrique **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations de jeton pour le gérer.

L'exemple ci-dessous montre comment instancier un **ServiceBusRestProxy** et appeler **ServiceBusRestProxy->createTopic** pour créer une rubrique nommée " mytopic " dans un espace de noms de service " MySBNamespace " :

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
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
> Vous pouvez utiliser la méthode <b>listQueues</b> sur les objets <b>ServiceBusRestProxy</b> pour vérifier s'il existe déjà une file d'attente avec un nom défini dans un espace de noms de service.

##<a id="SendMessages"></a> Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la méthode **ServiceBusRestProxy->sendQueueMessage**. Le code ci-dessous montre comment envoyer un message à la rubrique " myqueue " créée précédemment dans
l'espace de noms de service MySBNamespace.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Les messages envoyés aux files d'attente de Service Bus (et en provenance de celles-ci) sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de méthodes standard (telles que **getLabel**, **getTimeToLive**, **setLabel** et **setTimeToLive**) et des propriétés servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires.

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. La taille maximale de la file d'attente est de 5 Go.

##<a id="ReceiveMessages"></a> Réception des messages d'une file d'attente

Le moyen principal de recevoir les messages d'une file d'attente est d'utiliser une méthode **ServiceBusRestProxy->receiveQueueMessage**. Deux modes permettent de recevoir des messages : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : lorsque Service Bus reçoit une demande de lecture pour un message figurant dans une file d'attente, il marque le message comme étant consommé et le renvoie à l'application. Le mode**ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une
application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock**, la réception d'un message devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en transmettant le message reçu à **ServiceBusRestProxy->deleteMessage**. Lorsque Service Bus obtient l'appel **deleteMessage**, il marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités avec le mode **PeekLock** (différent du mode par défaut).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
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

Si l'application se bloque après le traitement du message, mais avant l'envoi de la demande **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, l'ajout d'une logique supplémentaire à votre application pour traiter la remise de messages en double est recommandé. Ceci est souvent obtenu grâce à la propriété **getMessageId** du message, qui reste constante pendant les tentatives de remise.

##<a id="NextSteps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez la rubrique MSDN [Files d'attente, rubriques et abonnements Service Bus][] pour plus d'informations.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Diagramme de file d'attente de Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Portail de gestion Azure]: http://manage.windowsazure.com/
[Capture d'écran du nœud Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[Capture d'écran Créer un espace de noms]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[Capture d'écran Available Namespaces]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[Capture d'écran Liste d'espaces de noms]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[Capture d'écran du volet Propriétés]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[Capture d'écran Clé par défaut]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once

