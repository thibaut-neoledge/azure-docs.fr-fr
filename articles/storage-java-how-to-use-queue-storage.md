<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="" solutions="" manager="" editor="" />

Utilisation du service de stockage de files d'attente à partir de Java
======================================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage des files d'attente Azure. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure pour Java](http://www.windowsazure.com/en-us/develop/java/). Les scénarios traités incluent l'insertion, la lecture furtive, la récupération et la suppression des messages de file d'attente, ainsi que la création et la suppression des files d'attente. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#NextSteps).

Sommaire
--------

-   [Présentation du stockage des files d'attente](#what-is)
-   [Concepts](#Concepts)
-   [Création d'un compte de stockage Azure](#CreateAccount)
-   [Création d'une application Java](#CreateApplication)
-   [Configuration de votre application pour accéder au stockage de files d'attente](#ConfigureStorage)
-   [Configuration d'une chaîne de connexion de stockage Azure](#ConnectionString)
-   [Création d'une file d'attente](#create-queue)
-   [Ajout d'un message à une file d'attente](#add-message)
-   [Lecture furtive du message suivant](#peek-message)
-   [Enlèvement du message suivant de la file d'attente](#dequeue-message)
-   [Modification du contenu d'un message en file d'attente](#change-message)
-   [Options supplémentaires pour l'enlèvement des messages](#additional-options)
-   [Obtention de la longueur de la file d'attente](#get-queue-length)
-   [Suppression d'une file d'attente](#delete-queue)
-   [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Création d'un compte de stockage Azure
--------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Création d'une application Java
-------------------------------

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local, ou dans le code s'exécutant dans un rôle Web ou un rôle de travail dans Azure. Nous partons du principe que vous avez téléchargé et installé le Kit de développement Java (JDK) et suivi les instructions figurant sur la page [Téléchargement du Kit de développement logiciel (SDK) Azure pour Java](http://www.windowsazure.com/en-us/develop/java/) pour installer les bibliothèques Azure pour Java et le Kit de développement logiciel (SDK) Azure, et que vous avez créé un compte de stockage Azure dans votre abonnement Azure. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes. Vous devez seulement être en mesure de compiler un projet Java et de référencer des bibliothèques Azure pour Java.

Configuration de votre application pour accéder au stockage de files d'attente
------------------------------------------------------------------------------

Ajoutez les instructions import suivantes au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux files d'attente :

    // Ajoutez les imports suivants pour utiliser des API de file d'attente
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

Configuration d'une chaîne de connexion de stockage Azure
---------------------------------------------------------

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d'identification permettant d'accéder aux services de gestion des données. Lors de l'exécution d'une application cliente, vous devez spécifier la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et la clé d'accès primaire pour le compte de stockage, répertoriés sur le portail de gestion pour les valeurs *AccountName* et *AccountKey*. Cet exemple vous montre comment déclarer un champ statique pour qu'il contienne une chaîne de connexion :

    // Définissez la chaîne de connexion à l'aide de vos valeurs
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Azure, cette chaîne peut être stockée dans le fichier de configuration de service ServiceConfiguration.cscfg et elle est accessible en appelant la méthode RoleEnvironment.getConfigurationSettings. Voici un exemple de code vous permettant d'extraire la chaîne de connexion à partir d'un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Création d'une file d'attente
-----------------------------

Un objet CloudQueueClient vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet CloudQueueClient.

Tous les codes de ce guide utilisent une chaîne de connexion de stockage déclarée de l'une des deux façons présentées ci-dessus. Plusieurs méthodes permettent aussi de créer des objets CloudStorageAccount. Pour plus d'informations, consultez la documentation Javadocs pour CloudStorageAccount.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

Utilisez l'objet CloudQueueClient pour obtenir une référence pointant vers la file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Créez la file d'attente si elle n'existe pas déjà
    queue.createIfNotExist();

Ajout d'un message à une file d'attente
---------------------------------------

Pour insérer un message dans une file d'attente existante, commencez par créer un CloudQueueMessage. Appelez ensuite la méthode addMessage. Un CloudQueueMessage peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World ».

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Créez la file d'attente si elle n'existe pas déjà
    queue.createIfNotExist();

    // Créez un message, puis ajoutez-le à la file d'attente
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

Lecture furtive du message suivant
----------------------------------

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant peekMessage.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Lisez furtivement le message suivant
    CloudQueueMessage peekedMessage = queue.peekMessage();

Enlèvement du message suivant de la file d'attente
--------------------------------------------------

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez retrieveMessage, vous obtenez le message suivant dans une file d'attente. Un message renvoyé par retrieveMessage devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler deleteMessage. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle deleteMessage juste après le traitement du message.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Extrayez le premier message visible dans la file d'attente
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Traitez le message en moins de 30 secondes, puis supprimez le message.
    queue.deleteMessage(retrievedMessage);

Modification du contenu d'un message en file d'attente
------------------------------------------------------

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de n fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message à chaque fois qu'il est traité.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Extrayez le premier message visible dans la file d'attente
    CloudQueueMessage message = queue.retrieveMessage();

    // Modifiez le contenu du message et définissez-le pour qu'il soit visible dans 60 secondes
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

Options supplémentaires pour l'enlèvement des messages
------------------------------------------------------

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message.

L'exemple de code suivant utilise la méthode retrieveMessages pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **for**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes (300 secondes) pour chaque message. Notez que le délai de cinq minutes démarre en même temps pour tous les messages, donc une fois les cinq minutes écoulées après l'appel de retrieveMessages, tous les messages n'ayant pas été supprimés redeviennent visibles.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Extrayez 20 messages de la file d'attente avec un délai d'expiration de la visibilité de 300 secondes
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Effectuez le traitement pour tous les messages en moins de 5 minutes, 
        // en supprimant chaque message après son traitement.
        queue.deleteMessage(message);
    }

Obtention de la longueur de la file d'attente
---------------------------------------------

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode downloadAttributes demande au service de File d'attente plusieurs valeurs actives, y compris le nombre de messages dans une file d'attente. Ce nombre est approximatif étant donné que des messages peuvent être ajoutés ou supprimés une fois que le service de File d'attente a répondu à votre demande. La méthode getApproximateMethodCount renvoie la dernière valeur extraite par l'appel à downloadAttributes, sans appeler le service de File d'attente.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Téléchargez le nombre de messages approximatif du serveur
    queue.downloadAttributes();

    // Extrayez le nombre de messages approximatif mis en cache récemment
    long cachedMessageCount = queue.getApproximateMessageCount();

Suppression d'une file d'attente
--------------------------------

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode delete sur l'objet queue.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Supprimez la file d'attente
    queue.delete();

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage des files d'attente, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure]
-   Accédez au blog de l'équipe Azure Storage : &lt;http://blogs.msdn.com/b/windowsazurestorage/&gt;

