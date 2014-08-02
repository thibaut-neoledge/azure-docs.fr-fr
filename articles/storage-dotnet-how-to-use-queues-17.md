<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service (2.0)" pageTitle="How to use the queue storage service | Microsoft Azure" metaKeywords="Get started Azure queue, Azure asynchronous processing, Azure queue, Azure queue storage, Azure queue .NET, Azure queue storage .NET, Azure queue C#, Azure queue storage C#" description="Learn how to use the Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Utilisation du service de stockage de files d'attente
=====================================================

[version 1.7](/en-us/develop/net/how-to-guides/queue-service-v17/ "version 1.7") [version 2.0](/en-us/develop/net/how-to-guides/queue-service/ "version 2.0")

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage des files d'attente Azure. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#next-steps).

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
-   [Enlèvement du message suivant de la file d'attente](#get-message)
-   [Utilisation d'options supplémentaires pour l'enlèvement des messages](#advanced-get)
-   [Obtention de la longueur de la file d'attente](#get-queue-length)
-   [Suppression d'une file d'attente](#delete-queue)
-   [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Création d'un compteCréation d'un compte de stockage Azure
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configuration d'une chaîne de connexionConfiguration d'une chaîne de connexion de stockage Azure
------------------------------------------------------------------------------------------------

L'API de stockage Azure .NET prend en charge l'utilisation d'une chaîne de connexion de stockage pour la configuration de points de terminaison et d'informations d'identification permettant d'accéder aux services de stockage. Vous pouvez placer votre chaîne de connexion de stockage dans un fichier de configuration, au lieu de la coder en dur dans le code :

-   Lorsque vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
-   Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier `web.config`).

Dans les deux cas, vous pouvez extraire votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, tel qu'indiqué plus loin dans ce guide.

### Configuration de votre chaîne de connexion lors de l'utilisation de Cloud Services

Le mécanisme de configuration de service est propre aux projets Azure Cloud Services et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier **Roles** de votre projet de déploiement Azure, cliquez avec le bouton droit sur votre rôle Web ou votre rôle de travail, puis cliquez sur **Properties**.
  
     ![Blob5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  Cliquez sur l'onglet **Settings**, puis sur le bouton **Add Setting**.

     ![Blob6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la grille des paramètres.

3.  Dans le menu déroulant **Type** de l'entrée **Setting1**, sélectionnez **Connection String**.

     ![Blob7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  Cliquez sur le bouton **...** situé à l'extrême droite de l'entrée **Setting1**. La boîte de dialogue **Storage Account Connection String** s'ouvre.

5.  Indiquez si vous voulez cibler l'émulateur de stockage (simulation de stockage Azure sur votre machine locale) ou un compte de stockage existant dans le cloud. Le code de ce guide fonctionne dans les deux cas. Si vous voulez stocker des données d'objet blob dans le compte de stockage créé précédemment dans Azure, entrez la valeur **Primary Access Key** copiée lors de l'étape précédente de ce didacticiel.

    ![Blob8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  Remplacez la valeur **Setting1** de l'entrée **Name** par un nom plus convivial, tel que **StorageConnectionString**. Le référencement de la chaîne de connexion dans le code sera effectué plus loin dans ce guide.

    ![Blob9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)

### Configuration de votre chaîne de connexion dans le cadre de l'utilisation de Sites Web Azure ou Azure Virtual Machines

Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config`). Stockez la chaîne de connexion en utilisant l'élément `<appSettings>` :

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Pour plus d'informations sur les chaînes de connexion de stockage, consultez la page [Configuration des chaînes de connexion](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx).

Vous pouvez à présent effectuer les tâches présentées dans ce guide.

Accès par programme Accès aux files d'attente par programme à l'aide de .NET
----------------------------------------------------------------------------

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C\# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Vous pouvez utiliser les types **CloudStorageAccount** et **CloudConfigurationManager** pour extraire votre chaîne de connexion de stockage, ainsi que vos informations de compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

Création d'une file d'attente Création d'une file d'attente
-----------------------------------------------------------

Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet **CloudQueueClient**. Tous les codes de ce guide utilisent une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**. Pour plus d'informations, consultez la documentation de [CloudStorageAccount](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx).

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Créez la file d'attente si elle n'existe pas déjà
    queue.CreateIfNotExist();

Insertion d'un message Insertion d'un message dans une file d'attente
---------------------------------------------------------------------

Pour insérer un message dans une file d'attente existante, commencez par créer un **CloudQueueMessage**. Appelez ensuite la méthode **AddMessage**. Un **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'**octets**. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World ».

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Créez la file d'attente si elle n'existe pas déjà
    queue.CreateIfNotExist();

    // Créez un message, puis ajoutez-le à la file d'attente
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

Lecture furtive du message suivant Lecture furtive du message suivant
---------------------------------------------------------------------

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode **PeekMessage**.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Lisez furtivement le message suivant
    CloudQueueMessage peekedMessage = queue.PeekMessage();

Modification du contenu d'un message Modification du contenu d'un message en file d'attente
-------------------------------------------------------------------------------------------

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message à chaque fois qu'il est traité.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible immédiatement
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

Enlèvement du message suivant de la file d'attente Enlèvement du message suivant de la file d'attente
-----------------------------------------------------------------------------------------------------

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez **GetMessage**, vous obtenez le message suivant dans une file d'attente. Un message renvoyé par **GetMessage** devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **DeleteMessage**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **DeleteMessage** juste après le traitement du message.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Obtenez le message suivant
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    // Traitez le message en moins de 30 secondes, puis supprimez le message
    queue.DeleteMessage(retrievedMessage);

Autres options d'enlèvement de la file d'attente des options supplémentaires pour l'enlèvement des messages
-----------------------------------------------------------------------------------------------------------

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **foreach**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées après l'appel de **GetMessages**, tous les messages n'ayant pas été supprimés redeviennent visibles.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Effectuez le traitement pour tous les messages en moins de 5 minutes, en supprimant chaque message après son traitement
        queue.DeleteMessage(message);
    }

Obtention de la longueur de la file d'attente Obtention de la longueur de la file d'attente
-------------------------------------------------------------------------------------------

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **RetrieveApproximateMessageCount** demande au service de File d'attente de compter le nombre de messages dans une file d'attente. Le nombre est uniquement une approximation, car des messages peuvent être ajoutés ou supprimés après la réponse du service de File d'attente à votre demande. La propriété **ApproximateMethodCount** renvoie la dernière valeur extraite par la méthode **RetrieveApproximateMessageCount**, sans appeler le service de File d'attente.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Extrayez le nombre de messages approximatif
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Extrayez le nombre de messages approximatif mis en cache
    int
     cachedMessageCount = queue.ApproximateMessageCount;

Suppression d'une file d'attente Suppression d'une file d'attente
-----------------------------------------------------------------

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **Delete** sur l'objet file d'attente.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Supprimez la file d'attente
    queue.Delete();

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage des files d'attente, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de File d'attente :
    -   [Référence de bibliothèque cliente .NET](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Référence de l'API REST](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
    -   Utilisez le [stockage de table](/en-us/develop/net/how-to-guides/table-services/) pour stocker des données structurées.
    -   Utilisez le [stockage d'objets blob](/en-us/develop/net/how-to-guides/blob-storage/) pour stocker des données non structurées.
    -   Utilisez une [base de données SQL](/en-us/develop/net/how-to-guides/sql-database/) pour stocker des données relationnelles.

