<properties 
	pageTitle="Utilisation des rubriques Service Bus avec PHP | Microsoft Azure" 
	description="Découvrez comment utiliser les rubriques Service Bus avec PHP dans Azure." 
	services="service-bus" 
	documentationCenter="php" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="sethm"/>


# Utilisation des rubriques et abonnements Service Bus

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article vous montre comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP](../php-download-sdk.md). Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements**, **création de filtres d'abonnement**, **envoi de messages à une rubrique**, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Création d'une application PHP

Le référencement de classes issues du [Kit de développement logiciel (SDK) Azure pour PHP](../php-download-sdk.md) dans votre code constitue la seule exigence pour créer une application PHP qui accède au service blob Azure. Vous pouvez utiliser tous les outils de développement pour créer votre application, ou Bloc-notes.

> [AZURE.NOTE] L’[extension OpenSSL](http://php.net/openssl) doit également être installée et activée dans votre installation PHP.

Cet article décrit comment utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP localement ou dans le code d’un rôle web, d’un rôle de travail ou d’un site web Azure.

## Obtention des bibliothèques clientes Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configuration de votre application pour l'utilisation de Service Bus

Utilisation des API Service Bus :

1. référencer le fichier de chargeur automatique à l'aide de l'instruction [require\_once][require-once].
2. référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServiceBusService**.

> [AZURE.NOTE] Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez référencer le fichier de chargeur automatique **WindowsAzure.php**.

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Dans les exemples suivants, l'instruction `require_once` s'affichera toujours, mais seules les classes nécessaires aux besoins de l'exemple à exécuter sont référencées.

## Configuration d’une connexion Service Bus

Pour instancier un client Azure Service Bus, vous devez disposer au préalable d'une chaîne de connexion valide au format suivant :

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

où `Endpoint` est généralement au format `https://[yourNamespace].servicebus.windows.net`.

Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

* Lui passer directement la chaîne de connexion.
* Utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
	* par défaut une source externe est prise en charge : variables d'environnement.
	* Vous pouvez ajouter de nouvelles sources via une extension de la classe **ConnectionStringSource**.

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
	
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## Création d'une rubrique

Vous pouvez effectuer des opérations de gestion pour les rubriques Service Bus via la classe **ServiceBusRestProxy**. Un objet **ServiceBusRestProxy** est construit via la méthode de fabrique **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations de jeton pour le gérer.

L’exemple suivant montre comment instancier un **ServiceBusRestProxy** et appeler **ServiceBusRestProxy->createTopic** pour créer une rubrique nommée `mytopic` dans un espace de noms `MySBNamespace` :

```
require_once 'vendor/autoload.php';

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
	// http://msdn.microsoft.com/library/windowsazure/dd179357
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] vous pouvez utiliser la méthode `listTopics` sur les objets `ServiceBusRestProxy` pour vérifier s'il existe déjà une rubrique d'un nom déterminé dans un espace de noms de service.

## Création d'un abonnement

Les abonnements de rubrique sont également créés à l'aide de la méthode **ServiceBusRestProxy->createSubscription**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement « mysubscription » qui est créé utilise le filtre par défaut **MatchAll**.

```
require_once 'vendor/autoload.php';

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
	// http://msdn.microsoft.com/library/azure/dd179357
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique. Parmi les types de filtre pris en charge par les abonnements, **SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d'informations sur SqlFilters, consultez la page [Propriété SqlExpression][sqlfilter].

> [AZURE.NOTE] Chaque règle d’un abonnement traite les messages entrants de façon indépendante, ajoutant leurs messages de résultat à l’abonnement. En outre, chaque nouvel abonnement dispose d’un objet **Règle** par défaut avec un filtre qui ajoute tous les messages de la rubrique à l’abonnement. Pour recevoir uniquement les messages correspondant à votre filtre, vous devez supprimer la règle par défaut. Vous pouvez supprimer la règle par défaut à l'aide de la méthode `ServiceBusRestProxy->deleteRule`.

Dans l'exemple suivant, l'abonnement **HighMessages** est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **MessageNumber** a une valeur supérieure à 3 (consultez la section [Envoi de messages à une rubrique](#send-messages-to-a-topic) pour plus d'informations sur l'ajout de propriétés personnalisées aux messages) :

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Notez que ce code requiert l'utilisation d'un espace de noms supplémentaire : `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

De même, l’exemple suivant crée l’abonnement **LowMessages** avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété **MessageNumber** a une valeur inférieure ou égale à 3 :

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

À présent, dès lors qu’un message est envoyé vers la rubrique `mytopic`, il est toujours remis aux destinataires abonnés à l’abonnement `mysubscription` et est remis de manière sélective aux destinataires abonnés aux abonnements `HighMessages` et `LowMessages` (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application appelle la méthode **ServiceBusRestProxy->sendTopicMessage**. Le code suivant montre comment envoyer un message à la rubrique `mytopic` créée plus haut dans l’espace de noms de service `MySBNamespace`.

```
require_once 'vendor/autoload.php';

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
	// http://msdn.microsoft.com/library/azure/hh780775
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

Les messages envoyés aux rubriques Service Bus sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés et de méthodes standard (telles que **getLabel**, **getTimeToLive**, **setLabel** et **setTimeToLive**), ainsi que des propriétés pouvant être utilisées pour conserver les propriétés personnalisées propres à une application. L'exemple suivant montre comment envoyer 5 messages test à la rubrique `mytopic` créée précédemment. La méthode **setProperty** est utilisée pour ajouter une propriété personnalisée (`MessageNumber`) à chaque message. Notez que la valeur de la propriété `MessageNumber` varie pour chaque message (cela peut être utilisé pour déterminer les abonnements qui le reçoivent, comme indiqué dans la section [Création d'un abonnement](#create-a-subscription)) :

```
for($i = 0; $i < 5; $i++){
	// Create message.
	$message = new BrokeredMessage();
	$message->setBody("my message ".$i);
			
	// Set custom property.
	$message->setProperty("MessageNumber", $i);
			
	// Send message.
	$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. La taille maximale de la rubrique est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][].

## Réception des messages d'un abonnement

Le meilleur moyen de recevoir des messages d'un abonnement consiste à utiliser une méthode **ServiceBusRestProxy->receiveSubscriptionMessage**. Les messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique. En d'autres termes, lorsque Service Bus reçoit une demande de lecture pour un message figurant dans un abonnement, il marque le message comme consommé et le renvoie à l'application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock**, la réception d'un message devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en transmettant le message reçu à **ServiceBusRestProxy->deleteMessage**. Lorsque Service Bus obtient l'appel **deleteMessage**, il marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment un message peut être reçu et traité avec le mode **PeekLock** (différent du mode par défaut).

```
require_once 'vendor/autoload.php';

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
	$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

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
	// http://msdn.microsoft.com/library/azure/hh780735
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour le message reçu (au lieu de la méthode **deleteMessage**). Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la demande **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire aux applications pour traiter la remise de messages en double, Ceci est souvent obtenu grâce à la méthode **getMessageId** du message, qui reste constante pendant les tentatives de remise.

## Suppression de rubriques et d'abonnements

Pour supprimer une rubrique ou un abonnement, utilisez la méthode **ServiceBusRestProxy->deleteTopic** ou **ServiceBusRestProxy->deleteSubscripton**, respectivement. Notez que la suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique.

L'exemple suivant montre comment supprimer une rubrique nommée `mytopic` et ses abonnements inscrits.

```
require_once 'vendor/autoload.php';

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
	// http://msdn.microsoft.com/library/azure/dd179357
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

La méthode **deleteSubscription** permet de supprimer un abonnement de façon indépendante :

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## Étapes suivantes

Les principes de base des files d’attente Service Bus étant appris, consultez [Files d’attente, rubriques et abonnements][] pour plus d’informations.

[Files d’attente, rubriques et abonnements]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Quotas Service Bus]: service-bus-quotas.md

<!---HONumber=AcomDC_0601_2016-->