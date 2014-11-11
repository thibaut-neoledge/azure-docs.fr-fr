<properties title="How to use the queue service (PHP) - Azure feature guide" pageTitle="How to use the queue service (PHP) | Microsoft Azure" metaKeywords="Azure Queue Service messaging PHP" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in PHP." documentationCenter="PHP" services="storage" authors="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Utilisation du service de file d'attente à partir de PHP

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de file d'attente Azure. Les exemples sont écrits au moyen de classes provenant du Kit de développement logiciel (SDK) Windows pour PHP. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation du stockage des files d'attente][Présentation du stockage des files d'attente]
-   [Concepts][Concepts]
-   [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
-   [Création d'une application PHP][Création d'une application PHP]
-   [Configuration de votre application pour le service de file d'attente][Configuration de votre application pour le service de file d'attente]
-   [Configuration d'une connexion Azure Storage][Configuration d'une connexion Azure Storage]
-   [Création d'une file d'attente][Création d'une file d'attente]
-   [Ajout d'un message à une file d'attente][Ajout d'un message à une file d'attente]
-   [Lecture furtive du message suivant][Lecture furtive du message suivant]
-   [Enlèvement du message suivant de la file d'attente][Enlèvement du message suivant de la file d'attente]
-   [Modification du contenu d'un message en file d'attente][Modification du contenu d'un message en file d'attente]
-   [Options supplémentaires pour la suppression des messages dans la file d'attente][Options supplémentaires pour la suppression des messages dans la file d'attente]
-   [Obtention de la longueur de la file d'attente][Obtention de la longueur de la file d'attente]
-   [Suppression d'une file d'attente][Suppression d'une file d'attente]
-   [Étapes suivantes][1]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <span id="create-account"></span></a>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="create-app"></span></a>Création d'une application PHP

La référence de classes dans le Kit de développement logiciel (SDK) Azure pour PHP constitue la seule exigence pour créer une application PHP qui accède au service de file d'attente Azure dans votre code. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

Dans ce guide, vous allez utiliser des fonctionnalités de service de File d'attente qui peuvent être appelées dans une application PHP en local, ou dans le code s'exécutant dans un rôle web, un rôle de travail ou un site web Azure.

## <span id="GetClientLibrary"></span></a>Obtention des bibliothèques clientes Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="configure-app"></span></a>Configuration de votre application pour accéder au service de file d'attente

Pour utiliser les API du service de file d'attente Azure, vous devez procéder comme suit :

1.  référencer le fichier de chargeur automatique à l'aide de l'instruction [require\_once][require\_once] ; et
2.  référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServicesBuilder**.

> [WACOM.NOTE]
> Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez faire référence au fichier de chargeur automatique `WindowsAzure.php` .

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Dans les exemples ci-dessous, l'instruction `require_once` sera toujours affichée, mais seules les classes nécessaires à l'exécution de l'exemple seront référencées.

## <span id="connection-string"></span></a>Configuration d'une chaîne de connexion de stockage Azure

Pour instancier un client de service de file d'attente Azure, vous devez disposer au préalable d'une chaîne de connexion valide. Le format de la chaîne de connexion du service de file d'attente est le suivant :

Pour accéder à un service en ligne :

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder au stockage de l'émulateur :

    UseDevelopmentStorage=true

Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

-   lui passer directement la chaîne de connexion ; ou
-   utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :

    -   par défaut une source externe est prise en charge : variables d'environnement ;
    -   de nouvelles sources peuvent être ajoutées via une extension de la classe **ConnectionStringSource**.

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

## <span id="create-queue"></span></a> Création d'une file d'attente

Un objet **QueueRestProxy** vous permet de créer une file d'attente avec la méthode **createQueue**. Lors de la création d'une file d'attente, vous pouvez définir des options sur cette dernière, mais vous n'y êtes pas obligé. L'exemple ci-dessous illustre comment définir des métadonnées dans une file d'attente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateQueueOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // OPTIONAL: Set queue metadata.
    $createQueueOptions = new CreateQueueOptions();
    $createQueueOptions->addMetaData("key1", "value1");
    $createQueueOptions->addMetaData("key2", "value2");

    try {
        // Create queue.
        $queueRestProxy->createQueue("myqueue", $createQueueOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [WACOM.NOTE]
> Ne tenez pas compte de la différence entre majuscules et minuscules pour les clés de métadonnées. Toutes les clés sont lues en minuscules sur le service.

## <span id="add-message"></span></a> Ajout d'un message à une file d'attente

Pour ajouter un message à une file d'attente, utilisez **QueueRestProxy-\>createMessage**. La méthode prend le nom de la file d'attente, le texte du message et les options du message (qui sont facultatives).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateMessageOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Create message.
        $builder = new ServicesBuilder();
        $queueRestProxy->createMessage("myqueue", "Hello World!");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="peek-message"></span></a> Lecture furtive du message suivant

Vous pouvez lire furtivement un ou plusieurs messages au début d'une file d'attente sans les supprimer de la file d'attente en appelant la méthode **QueueRestProxy-\>peekMessages**. Par défaut, la méthode **peekMessage** renvoie un seul message, mais vous pouvez modifier cette valeur avec la méthode **PeekMessagesOptions-\>setNumberOfMessages**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\PeekMessagesOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // OPTIONAL: Set peek message options.
    $message_options = new PeekMessagesOptions();
    $message_options->setNumberOfMessages(1); // Default value is 1.

    try {
        $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $messages = $peekMessagesResult->getQueueMessages();

    // View messages.
    $messageCount = count($messages);
    if($messageCount <= 0){
        echo "There are no messages.<br />";
    }
    else{
        foreach($messages as $message)  {
            echo "Peeked message:<br />";
            echo "Message Id: ".$message->getMessageId()."<br />";
            echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
            echo "Message text: ".$message->getMessageText()."<br /><br />";
        }
    }

## <span id="dequeue-message"></span></a> Enlèvement du message suivant de la file d'attente

Votre code supprime un message d'une file d'attente en deux étapes. Tout d'abord, vous appelez **QueueRestProxy-\>listMessages**, ce qui rend le message invisible pour tout autre code lisant à partir de la file d'attente. Par défaut, ce message restera invisible pendant 30 secondes (si le message n'est pas supprimé pendant cette période, il deviendra à nouveau visible dans la file d'attente). Pour finaliser la suppression du message de la file d'attente, vous devez appeler **QueueRestProxy-\>deleteMessage**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **deleteMessage** juste après le traitement du message.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Get message.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];

    /* ---------------------
        Process message.
       --------------------- */

    // Get message Id and pop receipt.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();

    try {
        // Delete message.
        $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="change-message"></span></a> Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente en appelant **QueueRestProxy-\>updateMessage**. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de n fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message à chaque fois qu'il est traité.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;   

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Get message.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];

    // Define new message properties.
    $new_message_text = "New message text.";
    $new_visibility_timeout = 5; // Measured in seconds. 

    // Get message Id and pop receipt.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();

    try {
        // Update message.
        $queueRestProxy->updateMessage("myqueue", 
                                    $messageId, 
                                    $popReceipt, 
                                    $new_message_text, 
                                    $new_visibility_timeout);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="additional-options"></span></a>Options supplémentaires pour la suppression des messages dans la file d'attente

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de la visibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **getMessages** pour obtenir 16 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **for**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\ListMessagesOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Set list message options. 
    $message_options = new ListMessagesOptions();
    $message_options->setVisibilityTimeoutInSeconds(300); 
    $message_options->setNumberOfMessages(16);

    // Get messages.
    try{
        $listMessagesResult = $queueRestProxy->listMessages("myqueue", 
                                                         $message_options); 
        $messages = $listMessagesResult->getQueueMessages(); 

        foreach($messages as $message){
            
            /* ---------------------
                Process message.
            --------------------- */
        
            // Get message Id and pop receipt.
            $messageId = $message->getMessageId();
            $popReceipt = $message->getPopReceipt();
            
            // Delete message.
            $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="get-queue-length"></span></a> Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **QueueRestProxy-\>getQueueMetadata** demande au service de file d'attente de renvoyer les métadonnées relatives à la file d'attente. Appeler la méthode **getApproximateMessageCount** sur l'objet renvoyé permet d'obtenir le nombre de messages figurant dans une file d'attente. Ce nombre est approximatif étant donné que des messages peuvent être ajoutés ou supprimés une fois que le service de file d'attente a répondu à votre demande.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Get queue metadata.
        $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
        $approx_msg_count = $queue_metadata->getApproximateMessageCount();
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    echo $approx_msg_count;

## <span id="delete-queue"></span></a> Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **QueueRestProxy-\>deleteQueue**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Delete queue.
        $queueRestProxy->deleteQueue("myqueue");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="next-steps"></span></a>Étapes suivantes

Maintenant que vous avez appris les principes de base du service de file d'attente Azure, suivez ces liens pour découvrir des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][Stockage et accessibilité des données dans Azure]
-   Accédez au blog de l'équipe Azure Storage : <http://blogs.msdn.com/b/windowsazurestorage/>

  [Étapes suivantes]: #NextSteps
  [Présentation du stockage des files d'attente]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #create-account
  [Création d'une application PHP]: #create-app
  [Configuration de votre application pour le service de file d'attente]: #configure-app
  [Configuration d'une connexion Azure Storage]: #connection-string
  [Création d'une file d'attente]: #create-queue
  [Ajout d'un message à une file d'attente]: #add-message
  [Lecture furtive du message suivant]: #peek-message
  [Enlèvement du message suivant de la file d'attente]: #dequeue-message
  [Modification du contenu d'un message en file d'attente]: #change-message
  [Options supplémentaires pour la suppression des messages dans la file d'attente]: #additional-options
  [Obtention de la longueur de la file d'attente]: #get-queue-length
  [Suppression d'une file d'attente]: #delete-queue
  [1]: #next-steps
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [get-client-libraries]: ../includes/get-client-libraries.md
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
