<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use the Windows Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="tamram" />

Utilisation du service de stockage des files d'attente à partir de .NET
=======================================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage des files d'attente Azure. Les exemples ont été écrits en code C\# et utilisent le client de stockage Azure pour .NET. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation du stockage des files d'attente](#what-is)
-   [Concepts](#concepts)
-   [Création d'un compte de stockage Azure](#create-account)
-   [Configuration d'une chaîne de connexion de stockage Azure](#setup-connection-string)
-   [Accès aux files d'attente par programme à l'aide de .NET](#access)
-   [Création d'une file d'attente](#create-queue)
-   [Insertion d'un message dans une file d'attente](#insert-message)
-   [Lecture furtive du message suivant](#peek-message)
-   [Modification du contenu d'un message en file d'attente](#change-contents)
-   [Enlèvement du message suivant](#get-message)
-   [Utilisation des options supplémentaires pour l'enlèvement des messages](#advanced-get)
-   [Obtention de la longueur de la file d'attente](#get-queue-length)
-   [Suppression d'une file d'attente](#delete-queue)
-   [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Création d'un compteCréation d'un compte de stockage Azure
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configuration d'une chaîne de connexionConfiguration d'une chaîne de connexion de stockage Azure
------------------------------------------------------------------------------------------------

La bibliothèque du client de stockage Azure pour .NET prend en charge l’utilisation d’une chaîne de connexion de stockage pour la configuration de points de terminaison et d’informations d’identification permettant d’accéder aux services de stockage. Vous pouvez placer votre chaîne de connexion de stockage dans un fichier de configuration, au lieu de la coder en dur dans le code :

-   Lorsque vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
-   Lorsque vous utilisez Sites Web Azure, Azure Virtual Machines ou que vous développez des applications .NET conçues pour être exécutées en dehors d'Azure, il est recommandé de stocker votre chaîne de connexion à l'aide du système de configuration .NET (par exemple, le fichier `web.config` ou `app.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, comme cela est présenté plus loin dans ce guide.

### Configuration de votre chaîne de connexion lors de l'utilisation de Cloud Services

Le mécanisme de configuration de service est propre aux projets Azure Cloud Services et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier **Rôles** de votre projet de déploiement Azure, cliquez avec le bouton droit sur votre rôle Web ou de travail, puis cliquez sur **Propriétés**.

    ![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  Cliquez sur l'onglet **Paramètres** et appuyez sur le bouton **Ajouter un paramètre**.

    ![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)

    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la grille des paramètres.

3.  Dans la liste déroulante **Type** de la nouvelle entrée **Setting1**, choisissez **Chaîne de connexion**.

    ![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  Cliquez sur le bouton **...** situé à l'extrême droite de l'entrée **Setting1**. La boîte de dialogue **Storage Account Connection String** s'ouvre.

5.  Indiquez si vous voulez cibler l'émulateur de stockage (simulation de stockage Azure sur votre machine locale) ou un compte de stockage existant dans le cloud. Le code de ce guide fonctionne dans les deux cas. Si vous voulez stocker des données de files d'attente dans le compte de stockage créé précédemment dans Azure, entrez la valeur **Primary Access Key** copiée lors de l'étape précédente de ce didacticiel.

    ![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  Remplacez la valeur **Setting1** de l'entrée **Name** par un nom plus convivial, tel que **StorageConnectionString**. Le référencement de la chaîne de connexion dans le code sera effectué plus loin dans ce guide.

    ![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)

### Configuration de votre chaîne de connexion en utilisant la configuration .NET

Si vous développez une application qui n'est pas un service cloud Azure (voir la section précédente), il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config` ou `app.config`). Ceci inclut Sites Web Azure ou Azure Virtual Machines, ainsi que les applications conçues pour être exécutées hors de Azure. Stockez la chaîne de connexion à l'aide de l'élément `<appSettings>` comme suit :

    <configuration>
		<appSettings>
          <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
		</appSettings\>
    </configuration>

Pour plus d'informations sur les chaînes de connexion de stockage, consultez la page [Configuration des chaînes de connexion](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758697.aspx).

Vous pouvez à présent effectuer les tâches présentées dans ce guide.

Accès par programme Accès aux files d'attente par programme à l'aide de .NET
----------------------------------------------------------------------------

### Obtention de l'assembly

Vous pouvez utiliser NuGet pour obtenir l'assembly `Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit sur votre projet dans l'**Explorateur de solutions**, puis sélectionnez **Manage NuGet Packages**. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

`Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable dans le [Centre de développement .NET](http://www.windowsazure.com/fr-fr/develop/net/#). L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

### Déclarations d'espace de noms

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C\# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Assurez-vous de bien référencer l'assembly `Microsoft.WindowsAzure.Storage.dll`.

### Extraction de votre chaîne de connexion

Vous pouvez utiliser le type **CloudStorageAccount** pour représenter vos informations de compte de stockage. Si vous utilisez un modèle de projet Azure et/ou qu'une référence pointe vers Microsoft.WindowsAzure.CloudConfigurationManager, vous pouvez utiliser le type **CloudConfigurationManager** pour extraire votre chaîne de connexion de stockage, ainsi que vos informations de compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour extraire la chaîne de connexion. Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dépendances ODataLib

Les dépendances ODataLib de la bibliothèque cliente de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib sont [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2) et [Spatial](http://nuget.org/packages/System.Spatial/5.0.2).

Création d'une file d'attente Création d'une file d'attente
-----------------------------------------------------------

Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet **CloudQueueClient**. Tous les codes de ce guide utilisent une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**. Pour plus d'informations, consultez la documentation de [CloudStorageAccount](http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx).

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

Insertion d'un message Insertion d'un message dans une file d'attente
---------------------------------------------------------------------

Pour insérer un message dans une file d'attente existante, commencez par créer un **CloudQueueMessage**. Appelez ensuite la méthode **AddMessage**. Un **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'**octets**. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World » :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

Lecture furtive du message suivant Lecture furtive du message suivant
---------------------------------------------------------------------

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode **PeekMessage**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

Modification du contenu d'un message Modification du contenu d'un message en file d'attente
-------------------------------------------------------------------------------------------

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message à chaque fois qu'il est traité.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

Enlèvement du message suivant de la file d'attente Enlèvement du message suivant de la file d'attente
-----------------------------------------------------------------------------------------------------

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez **GetMessage**, vous obtenez le message suivant dans une file d'attente. Un message renvoyé par **GetMessage** devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **DeleteMessage**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **DeleteMessage** juste après le traitement du message.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

Autres options d'enlèvement de la file d'attente Utilisation d'options supplémentaires pour l'enlèvement des messages
---------------------------------------------------------------------------------------------------------------------

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **foreach**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées après l'appel de **GetMessages**, tous les messages n'ayant pas été supprimés redeviennent visibles.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

Obtention de la longueur de la file d'attente Obtention de la longueur de la file d'attente
-------------------------------------------------------------------------------------------

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **FetchAttributes** demande au service de files d'attente d'extraire les attributs de la file d'attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** renvoie la dernière valeur extraite par la méthode **FetchAttributes**, sans appeler le service de files d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int
     cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

Suppression d'une file d'attente Suppression d'une file d'attente
-----------------------------------------------------------------

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **Delete** sur l'objet file d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage des files d'attente, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services de files d'attente :
    -   [Référence de la bibliothèque cliente de stockage pour .NET](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn495001(v=azure.10).aspx)
    -   [Référence de l'API REST](http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355)
-   Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx).
-   Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
    -   Utilisez le [stockage de table](/en-us/develop/net/how-to-guides/table-services/) pour stocker des données structurées.
    -   Utilisez le [stockage d'objets blob](/en-us/develop/net/how-to-guides/blob-storage/) pour stocker des données non structurées.
    -   Utilisez une [base de données SQL](/en-us/develop/net/how-to-guides/sql-database/) pour stocker des données relationnelles.

