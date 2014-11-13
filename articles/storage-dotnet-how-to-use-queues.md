<properties urlDisplayName="Queue Service" pageTitle="Utilisation du stockage de files d'attente &agrave; partir de .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="D&eacute;couvrez comment utiliser le stockage de files d'attente Microsoft Azure pour cr&eacute;er et supprimer des files d'attente, et ins&eacute;rer, lire, obtenir et supprimer les messages en file d'attente." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="Utilisation du stockage de files d'attente Microsoft" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Utilisation du service de stockage de files d'attente à partir de .NET

Ce guide décrit le déroulement de scénarios courants dans le cadre de
l'utilisation du service de stockage de files d'attente Azure. Les exemples ont été écrits en code C#
et utilisent le client de stockage Azure pour .NET. Les scénarios traités incluent l'**insertion**, la
**lecture furtive**, la
**récupération** et la
**suppression** des messages de file d'attente, ainsi que la
**création et suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez
[la section Étapes suivantes][la section Étapes suivantes].

> [WACOM.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d'utiliser la version 4.x disponible via [NuGet][NuGet] ou dans le [Kit de développement logiciel (SDK) Azure pour .NET][Kit de développement logiciel (SDK) Azure pour .NET]. Pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage, consultez la rubrique [Accès au stockage de files d'attente par programme][Accès au stockage de files d'attente par programme].

## Sommaire

-   [Présentation du stockage des files d'attente][Présentation du stockage des files d'attente]
-   [Concepts][Concepts]
-   [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
-   [Configuration d'une chaîne de connexion de stockage Azure][Configuration d'une chaîne de connexion de stockage Azure]
-   [Accès au stockage de files d'attente par programme][Accès au stockage de files d'attente par programme]
-   [Création d'une file d'attente][Création d'une file d'attente]
-   [Insertion d'un message dans une file d'attente][Insertion d'un message dans une file d'attente]
-   [Lecture furtive du message suivant][Lecture furtive du message suivant]
-   [Modification du contenu d'un message en file d'attente][Modification du contenu d'un message en file d'attente]
-   [Enlèvement du message suivant de la file d'attente][Enlèvement du message suivant de la file d'attente]
-   [Utilisation d'options supplémentaires pour l'enlèvement des messages][Utilisation d'options supplémentaires pour l'enlèvement des messages]
-   [Obtention de la longueur de la file d'attente][Obtention de la longueur de la file d'attente]
-   [Suppression d'une file d'attente][Suppression d'une file d'attente]
-   [Étapes suivantes][la section Étapes suivantes]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">Création d’un compte</span>Création d’un compte de stockage Azure

</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">Configuration d'une chaîne de connexion</span>Configuration d'une chaîne de connexion de stockage Azure

</h2>
[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Accès par programme</span> Accès au stockage de files d'attente par programme

### Obtention de l’assembly

Vous pouvez utiliser NuGet pour obtenir l’assembly `Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**, puis sélectionnez **Manage NuGet Packages**. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

`Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable à partir du [Centre de développement .NET][Centre de développement .NET]. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code>.

### Déclarations d’espace de noms

Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C#
dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Assurez-vous de bien référencer l’assembly `Microsoft.WindowsAzure.Storage.dll`.

### Récupération de votre chaîne de connexion

Vous pouvez utiliser le type **CloudStorageAccount** pour représenter
les informations de votre compte de stockage. Si vous utilisez un modèle de projet Windows Azure
et/ou qu'une référence pointe vers
Microsoft.WindowsAzure.CloudConfigurationManager, vous
pouvez utiliser le type **CloudConfigurationManager**
pour extraire votre chaîne de connexion de stockage, ainsi que les informations
de votre compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion. Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dépendances ODataLib

Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib sont [OData][OData], [Edm][Edm] et [Spatial][Spatial].

## <a name="create-queue"></a><span class="short-header">Création d'une file d'attente</span> Création d'une file d'attente

Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente.
Le code suivant permet de créer un objet **CloudQueueClient**. Tous les codes de
ce guide utilisent une chaîne de connexion de stockage stockée dans la
configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet
**CloudStorageAccount**. Pour plus d'informations, consultez la documentation de [CloudStorageAccount][CloudStorageAccount]
.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la
file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">Insertion d'un message</span> Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente existante, commencez par créer un
**CloudQueueMessage**. Appelez ensuite la méthode **AddMessage**. Un
**CloudQueueMessage** peut être créé à partir d'une chaîne (au format
UTF-8) ou d'un tableau d'**octets**. Voici le code qui crée une file d'attente (si elle n'existe pas)
et insère le message « Hello, World » :

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

## <a name="peek-message"></a><span class="short-header">Lecture furtive du message suivant</span> Lecture furtive du message suivant

Vous pouvez lire le message en début de file d'attente sans le supprimer
de la file d'attente en appelant la méthode **PeekMessage**.

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

## <a name="change-contents"></a><span class="short-header">Modification du contenu d'un message</span> Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le
message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour
l'état de la tâche. Le code suivant met à jour le message de la file d'attente
avec un nouveau contenu et ajoute 60 secondes au
délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et
accorde une minute supplémentaire au client pour traiter le message. Vous pouvez
utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages
de file d'attente, sans devoir reprendre du début
si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi
un nombre de nouvelles tentatives et si le message est
retenté plus de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application
par un message à chaque fois qu'il est traité.

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

## <a name="get-message"></a><span class="short-header">Enlèvement du message suivant de la file d'attente</span> Enlèvement du message suivant de la file d'attente

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez
**GetMessage**, vous obtenez le message suivant dans une file d'attente. Un message renvoyé par
 **GetMessage** devient invisible par les autres codes lisant les
messages de cette file d'attente. Par défaut, ce message reste invisible pendant
30 secondes. Pour finaliser la suppression du message de la file d'attente,
vous devez aussi appeler **DeleteMessage**. Ce processus de suppression d'un message en deux étapes
garantit que, si votre code ne parvient pas à traiter un message à cause d'une
défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message
et réessayer. Votre code appelle **DeleteMessage** juste après le
traitement du message.

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

## <a name="advanced-get"></a><span class="short-header">Autres options d'enlèvement de la file d'attente</span> Utilisation d'options supplémentaires pour l'enlèvement des messages

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente.
Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un
délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins
de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode
**GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message
à l'aide d'une boucle **foreach**. Il définit également le délai d'expiration de l'invisibilité
sur cinq minutes pour chaque message. Notez que le délai de 5 minutes
démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées
après l'appel de **GetMessages**, tous les messages n'ayant pas été supprimés
redeviennent visibles.

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

## <a name="get-queue-length"></a><span class="short-header">Obtention de la longueur de la file d'attente</span> Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode
**FetchAttributes** demande au service de File d'attente
d'extraire les attributs de la file d'attente, y compris le nombre de messages. La propriété **ApproximateMethodCount**
 renvoie la dernière valeur extraite par la méthode
**FetchAttributes**, sans appeler le service de File d'attente.

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
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-queue"></a><span class="short-header">Suppression d'une file d'attente</span> Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode
**Delete** sur l'objet file d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage de files d'attente,
consultez les liens suivants pour apprendre à effectuer des tâches de stockage plus complexes.

-   Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services de files d'attente :
    -   [Référence de la bibliothèque de client de stockage pour .NET][Référence de la bibliothèque de client de stockage pour .NET]
    -   [Référence d’API REST][Référence d’API REST]
-   Pour plus d’informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page [Stockage et accessibilité des données dans Windows Azure][Stockage et accessibilité des données dans Windows Azure].
-   Pour apprendre à utiliser Azure Storage dans les processus principaux relatifs à Sites Web Azure, consultez la page [Prise en main du Kit de développement logiciel (SDK) Tâches web Azure][Prise en main du Kit de développement logiciel (SDK) Tâches web Azure].
-   Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.
    -   Utilisez le [stockage de table][stockage de table] pour stocker des données structurées.
    -   Utilisez le [stockage d’objets blob][stockage d’objets blob] pour stocker des données non structurées.
    -   Utilisez une [base de données SQL][base de données SQL] pour stocker des données relationnelles.

  [la section Étapes suivantes]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Kit de développement logiciel (SDK) Azure pour .NET]: /fr-fr/downloads/
  [Accès au stockage de files d'attente par programme]: #configure-access
  [Présentation du stockage des files d'attente]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #create-account
  [Configuration d'une chaîne de connexion de stockage Azure]: #setup-connection-string
  [Création d'une file d'attente]: #create-queue
  [Insertion d'un message dans une file d'attente]: #insert-message
  [Lecture furtive du message suivant]: #peek-message
  [Modification du contenu d'un message en file d'attente]: #change-contents
  [Enlèvement du message suivant de la file d'attente]: #get-message
  [Utilisation d'options supplémentaires pour l'enlèvement des messages]: #advanced-get
  [Obtention de la longueur de la file d'attente]: #get-queue-length
  [Suppression d'une file d'attente]: #delete-queue
  [Centre de développement .NET]: http://www.windowsazure.com/fr-fr/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Référence de la bibliothèque de client de stockage pour .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Référence d’API REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355
  [Stockage et accessibilité des données dans Windows Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [Prise en main du Kit de développement logiciel (SDK) Tâches web Azure]: /fr-fr/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [stockage de table]: /fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/
  [stockage d’objets blob]: /fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/
  [base de données SQL]: /fr-fr/documentation/articles/sql-database-dotnet-how-to-use/
