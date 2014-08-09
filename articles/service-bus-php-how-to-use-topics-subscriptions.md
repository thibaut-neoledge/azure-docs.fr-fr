<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="" solutions="" manager="" editor="" />

Utilisation des rubriques/abonnements Service Bus
=================================================

Ce guide vous montre comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements**, **création de filtres d'abonnement**, **envoi de messages à une rubrique**, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**.

Sommaire
--------

-   [Présentation des rubriques et des abonnements Service Bus](#what-are-service-bus-topics)
-   [Création d'un espace de noms de service](#create-a-service-namespace)
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms](#obtain-default-credentials)
-   [Création d'une application PHP](#CreateApplication)
-   [Obtention des bibliothèques clientes Azure](#GetClientLibrary)
-   [Configuration de votre application pour l'utilisation de Service Bus](#ConfigureApp)
-   [Création d'une rubrique](#CreateTopic)
-   [Création d'un abonnement](#CreateSubscription)
-   [Envoi de messages à une rubrique](#SendMessage)
-   [Réception des messages d'un abonnement](#ReceiveMessages)
-   [Gestion des blocages d'application et des messages illisibles](#HandleCrashes)
-   [Suppression de rubriques et d'abonnements](#DeleteTopicsAndSubscriptions)
-   [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

Création d'une application PHP
------------------------------

La référence de classes dans le [Kit de développement logiciel (SDK) Azure pour PHP](http://go.microsoft.com/fwlink/?LinkId=252473) constitue la seule exigence pour créer une application PHP qui accède au service Blob Azure dans votre code. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

> [WACOM.NOTE] 
> L'[extension OpenSSL](http://php.net/openssl) doit également être installée et activée dans votre installation PHP.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP en local, ou dans le code s'exécutant dans un rôle Web, un rôle de travail ou un site Web Azure.

Obtention des bibliothèques clientes Azure
------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Configuration de votre application pour l'utilisation de Service Bus
--------------------------------------------------------------------

Pour utiliser les API de rubrique Azure Service Bus, vous devez procéder comme suit :

1.  référencer le fichier de chargeur automatique à l'aide de l'instruction [require\_once](http://php.net/require_once) ; et
2.  référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServiceBusService**.

    > [WACOM.NOTE]
    > Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez référencer le fichier de chargeur automatique <code>WindowsAzure.php</code>.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Dans les exemples ci-dessous, l'instruction `require_once` s'affichera toujours, mais seules les classes nécessaires aux besoins de l'exemple à exécuter sont référencées.

Configuration d'une connexion Azure Service Bus
-----------------------------------------------

Pour instancier un client Azure Service Bus, vous devez disposer au préalable d'une chaîne de connexion valide au format suivant :

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

où le point de terminaison est généralement au format `https://[yourNamespace].servicebus.windows.net`.

Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

-   lui passer directement la chaîne de connexion ; ou
-   utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    -   par défaut une source externe est prise en charge : variables d'environnement ;
    -   de nouvelles sources peuvent être ajoutées via une extension de la classe **ConnectionStringSource**.

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

Création d'une rubrique
-----------------------

Vous pouvez effectuer des opérations de gestion pour les rubriques Service Bus via la classe **ServiceBusRestProxy**. Un objet **ServiceBusRestProxy** est construit via la méthode de fabrique **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations de jeton pour le gérer.

L'exemple ci-dessous montre comment instancier un **ServiceBusRestProxy** et appeler **ServiceBusRestProxy-\>createTopic** pour créer une rubrique nommée `mytopic` dans un espace de noms de service `MySBNamespace` :

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\TopicInfo;

    // Créez le proxy REST Service Bus.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Créez la rubrique.
        $topicInfo = new TopicInfo("mytopic");
        $serviceBusRestProxy->createTopic($topicInfo);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    > [WACOM.NOTE]
    > Vous pouvez utiliser la méthode <b>listTopics</b> sur les objets <b>ServiceBusRestProxy</b> pour vérifier s'il existe déjà une rubrique d'un certain nom dans un espace de noms de service.

Création d'un abonnement
------------------------

Les abonnements de rubrique sont également créés à l'aide de la méthode **ServiceBusRestProxy-\>createSubscription**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement « mysubscription » qui est créé utilise le filtre par défaut **MatchAll**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

    // Créez le proxy REST Service Bus.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        // Créez l'abonnement.
        $subscriptionInfo = new SubscriptionInfo("mysubscription");
        $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique. Parmi les types de filtres pris en charge par les abonnements, **SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d'informations sur SqlFilters, consultez la page [Propriété SqlExpression](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

    > [WACOM.NOTE]
    > Chaque règle d'un abonnement traite les messages entrants de façon indépendante, ajoutant leurs messages de résultat à l'abonnement. En outre, chaque nouvel abonnement dispose d'une <b>Règle</b> par défaut avec un filtre qui ajoute tous les messages de la rubrique à l'abonnement. Pour recevoir uniquement les messages correspondant à votre filtre, vous devez supprimer la règle par défaut. Vous pouvez supprimer la règle par défaut en utilisant la méthode <b>ServiceBusRestProxy->deleteRule</b>.

Dans l'exemple ci-dessous, l'abonnement « HighMessages » est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **MessageNumber** a une valeur supérieure à 3 (consultez la section [Envoi de messages à une rubrique](#SendMessage) pour plus d'informations sur l'ajout de propriétés personnalisées aux messages) :

    $subscriptionInfo = new SubscriptionInfo("HighMessages");
	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

    $serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');
	
	$ruleInfo = new RuleInfo("HighMessagesRule"); 
	$ruleInfo->withSqlFilter("MessageNumber > 3"); 
	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Notez que le code ci-dessus requiert l'utilisation d'un espace de noms supplémentaire : `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

De même, l'exemple suivant crée l'abonnement « LowMessages » avec un filtre SqlFilter qui sélectionne uniquement les messages dont la propriété MessageNumber a une valeur inférieure ou égale à 3 :

    $subscriptionInfo = new SubscriptionInfo("LowMessages");
	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

    $serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

	$ruleInfo = new RuleInfo("LowMessagesRule"); 
	$ruleInfo->withSqlFilter("MessageNumber <= 3"); 
	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

À présent, dès lors qu'un message est envoyé vers la rubrique `mytopic`, il est toujours remis aux destinataires abonnés à l'abonnement `mysubscription` et est remis de manière sélective aux destinataires abonnés aux abonnements « HighMessages » et « LowMessages » (en fonction du contenu du message).

Envoi de messages à une rubrique
--------------------------------

Pour envoyer un message à une rubrique Service Bus, votre application appelle la méthode **ServiceBusRestProxy-\>sendTopicMessage**. Le code ci-dessous montre comment envoyer un message à la rubrique `mytopic` créée précédemment à l'aide de l'espace de noms de service `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\BrokeredMessage;

    // Créez le proxy REST Service Bus.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Créez le message.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Envoyez le message.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Les messages envoyés aux rubriques Service Bus sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés et de méthodes standard (telles que **getLabel**, **getTimeToLive**, **setLabel** et **setTimeToLive**), ainsi que des propriétés pouvant être utilisées pour conserver les propriétés personnalisées propres à une application. L'exemple suivant montre comment envoyer cinq messages de test à la rubrique `mytopic` créée précédemment. La méthode **setProperty** est utilisée pour ajouter une propriété personnalisée (`MessageNumber`) à chaque message. Notez que la valeur de la propriété `MessageNumber` varie pour chaque message (cela peut être utilisé pour déterminer les abonnements qui le reçoivent, comme indiqué dans la section [Création d'un abonnement](#CreateSubscription) ci-dessus) :

    for($i = 0; $i < 5; $i++){
        // Créez le message.
        $message = new BrokeredMessage();
        $message->setBody("my message ".$i);
            
        // Définissez la propriété personnalisée.
        $message->setProperty("MessageNumber", $i);
            
        // Envoyez le message.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. La taille maximale de la file d'attente est de 5 Go.

Réception des messages d'un abonnement
--------------------------------------

Le moyen principal de recevoir des messages d'un abonnement consiste à utiliser une méthode **ServiceBusRestProxy-\>receiveSubscriptionMessage**. Ces messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : lorsque Service Bus reçoit une demande de lecture pour un message figurant dans un abonnement, il marque le message comme étant consommé et le renvoie à l'application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock**, la réception d'un message devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en transmettant le message reçu à **ServiceBusRestProxy-\>deleteMessage**. Lorsque Service Bus obtient l'appel **deleteMessage**, il marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités avec le mode **PeekLock** (différent du mode par défaut).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

    // Créez le proxy REST Service Bus.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Définissez le mode de réception sur PeekLock (ReceiveAndDelete par défaut)
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();

        // Obtenez le message.
        $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
                                                                    "mysubscription", 
                                                                    $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Traitez le message ici.
        ----------------------------*/
        
        // Supprimez le message. Non requis si peek lock n'est pas défini.
        echo "Deleting message...<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici :
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Gestion des blocages d'application et des messages illisibles
-------------------------------------------------------------

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour le message reçu (au lieu de la méthode **deleteMessage**). Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la demande **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Ceci est souvent obtenu grâce à la propriété **getMessageId** du message, qui reste constante pendant les tentatives de remise.

Suppression des rubriques et des abonnements
--------------------------------------------

Pour supprimer une rubrique ou un abonnement, utilisez la méthode **ServiceBusRestProxy-\>deleteTopic** ou **ServiceBusRestProxy-\>deleteSubscripton** respectivement. Notez que la suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique.

L'exemple suivant montre comment supprimer une rubrique (`mytopic`) et ses abonnements inscrits.

    require_once 'vendor\autoload.php';

    use WindowsAzure\ServiceBus\ServiceBusService;
    use WindowsAzure\ServiceBus\ServiceBusSettings;
    use WindowsAzure\Common\ServiceException;

    // Créez le proxy REST Service Bus.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Supprimez la rubrique.
        $serviceBusRestProxy->deleteTopic("mytopic");
    }
    catch(ServiceException $e){
        // Gérez une exception en fonction des codes et des messages d'erreur.
        // Les codes et messages d'erreur se trouvent ici : 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

La méthode **deleteSubscription** permet de supprimer un abonnement de façon indépendante :

    $serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

Étapes suivantes
----------------

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez la page [Files d'attente, rubriques et abonnements Service Bus](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx) pour plus d'informations.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[What are Service Bus Topics and Subscriptions?]: #bkmk_WhatAreSvcBusTopics
[Create a Service Namespace]: #bkmk_CreateSvcNamespace
[Obtain the Default Management Credentials for the Namespace]: #bkmk_ObtainDefaultMngmntCredentials
[Configure Your Application to Use Service Bus]: #bkmk_ConfigYourApp
[How to: Create a Topic]: #bkmk_HowToCreateTopic
[How to: Create Subscriptions]: #bkmk_HowToCreateSubscrip
[How to: Send Messages to a Topic]: #bkmk_HowToSendMsgs
[How to: Receive Messages from a Subscription]: #bkmk_HowToReceiveMsgs
[How to: Handle Application Crashes and Unreadable Messages]: #bkmk_HowToHandleAppCrash
[How to: Delete Topics and Subscriptions]: #bkmk_HowToDeleteTopics
[Next Steps]: #bkmk_NextSteps
[Service Bus Topics diagram]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Azure Management Portal]: http://manage.windowsazure.com/
[Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[Create a New Namespace screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
[Available Namespaces screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once
