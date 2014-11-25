<properties urlDisplayName="Queue Service" pageTitle="Utilisation du stockage de files d'attente à partir de .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Utilisation du service de stockage de files d'attente à partir de .NET

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation
du service de stockage de files d'attente Azure. Les exemples sont écrits en code C\#
et utilisent le client de stockage Azure pour .NET Les scénarios traités incluent **l'insertion**,
**la lecture furtive**, **la récupération** et **la suppression** des messages de file d'attente, ainsi que
**la création et la suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez
la section [Étapes suivantes][Étapes suivantes].

> [WACOM.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d'utiliser la version 4.x disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou dans le [Kit de développement logiciel (SDK) Azure pour .NET](/fr-fr/downloads/). Pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage, consultez la rubrique [ Accès au stockage de files d'attente par programme][ Accès au stockage de files d'attente par programme].

<h2>Sommaire</h2>

-   [Présentation du stockage des files d'attente][Présentation du stockage des files d'attente]
-   [Concepts][Concepts]
-   [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
-   [Configuration d'une chaîne de connexion de stockage Azure][Configuration d'une chaîne de connexion de stockage Azure]
-   [ Accès au stockage de files d'attente par programme][ Accès au stockage de files d'attente par programme]
-   [ Création d'une file d'attente][ Création d'une file d'attente]
-   [ Insertion d'un message dans une file d'attente][ Insertion d'un message dans une file d'attente]
-   [ Lecture furtive du message suivant][ Lecture furtive du message suivant]
-   [ Modification du contenu d'un message en file d'attente][ Modification du contenu d'un message en file d'attente]
-   [ Enlèvement du message suivant de la file d'attente][ Enlèvement du message suivant de la file d'attente]
-   [ Utilisation d'options supplémentaires pour l'enlèvement des messages][ Utilisation d'options supplémentaires pour l'enlèvement des messages]
-   [ Obtention de la longueur de la file d'attente][ Obtention de la longueur de la file d'attente]
-   [ Suppression d'une file d'attente][ Suppression d'une file d'attente]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a><span  class="short-header">Création d'un compte</span>Création d'un compte de stockage Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span  class="short-header">Configuration d'une chaîne de connexion</span>Configuration d'une chaîne de connexion de stockage Azure</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Accès par programme</span> Accès au stockage de files d'attente par programme

<h3>Obtention de l'assembly</h3>
Vous pouvez utiliser NuGet pour obtenir l'assembly " Microsoft.WindowsAzure.Storage.dll ". Cliquez avec le bouton droit sur votre projet dans l'**Explorateur de solutions**, puis sélectionnez **Manage NuGet Packages**.  Effectuez une recherche en ligne sur " WindowsAzure.Storage ", puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

`Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable dans le <a href="http://www.windowsazure.com/fr-fr/develop/net/#">Centre de développement .NET</a>. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Déclarations d'espace de noms</h3>
Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C\#
dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Assurez-vous de bien référencer l'assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Récupération de votre chaîne de connexion</h3>
Vous pouvez utiliser le type **CloudStorageAccount** pour représenter 
les informations de votre compte de stockage. Si vous utilisez un 
modèle de projet Windows Azure et/ou qu'une référence pointe vers l'espace de noms 
Microsoft.WindowsAzure.CloudConfigurationManager, vous 
pouvez utiliser le type **CloudConfigurationManager** 
pour extraire votre chaîne de connexion de stockage, ainsi que les informations de
votre compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion.  Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dépendances ODataLib</h3>
Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF.  Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet.  Les packages ODataLib sont [OData], [Edm] et [Spatial].

<h2><a name="create-queue"></a><span  class="short-header">Création d'une file d'attente</span> Création d'une file d'attente</h2>

Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente.
Le code suivant crée un objet **CloudQueueClient**. Tous les codes de
ce guide utilisent une chaîne de connexion de stockage stockée dans la
configuration de service de l'application Azure. Plusieurs méthodes permettent aussi de créer
un objet **CloudStorageAccount**. Pour plus d'informations, consultez la documentation de [CloudStorageAccount][CloudStorageAccount]
.

    // Récupérez un compte de stockage à partir d'une chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la file d'attente
à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Créez la file d'attente si elle n'existe pas déjà
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Insertion d'un message</span> Insertion d'un message dans une file d'attente</h2>

Pour insérer un message dans une file d'attente existante, commencez par créer un
**CloudQueueMessage**. Appelez ensuite la méthode **AddMessage**. Une
**CloudQueueMessage** peut être créé à partir d'une chaîne (au format
UTF-8) ou d'un tableau d'**octets**. Voici le code qui crée une file d'attente (si elle
n'existe pas) et insère le message " Hello, World " :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Créez la file d'attente si elle n'existe pas déjà.
    queue.CreateIfNotExists();

    // Créez un message, puis ajoutez-le à la file d'attente.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">Lecture furtive du message suivant</span> Lecture furtive du message suivant</h2>

Vous pouvez lire furtivement le message au début de la file d'attente sans le supprimer
de la file d'attente en appelant la méthode **PeekMessage**.

    // Récupérez un compte de stockage à partir d'une chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Lisez furtivement le message suivant
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Affichez le message.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a><span  class="short-header">Modification du contenu d'un message</span> Modification du contenu d'un message en file d'attente</h2>

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le
message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour
l'état de la tâche. Le code suivant met à jour le message de la file d'attente
avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de
la visibilité. Cette opération enregistre l'état de la tâche associée au message et
accorde une minute supplémentaire au client pour traiter le message. Vous
pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de
file d'attente, sans devoir reprendre du début si une
étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement,
vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus
de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application
par un message à chaque fois qu'il est traité.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Obtenez le message de la file d'attente et mettez à jour le contenu du message.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Rendez-le visible immédiatement.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Enlèvement du message suivant de la file d'attente</span> Enlèvement du message suivant de la file d'attente</h2>

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez
**GetMessage**, vous obtenez le message suivant dans une file d'attente. Un message renvoyé
par **GetMessage** devient invisible par les autres codes lisant les messages
de cette file d'attente. Par défaut, ce message reste invisible pendant 30
secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi
appeler **DeleteMessage**. Ce processus de suppression d'un message
garantit que, si votre code ne parvient pas à traiter un message à cause d'une
défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message
et réessayer. Votre code appelle **DeleteMessage** juste après le traitement
du message.

    // Récupérez un compte de stockage à partir d'une chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Obtenez le message suivant
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    // Traitez le message en moins de 30 secondes, puis supprimez le message
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a><span  class="short-header">Autres options d'enlèvement de la file d'attente</span> Utilisation d'options supplémentaires pour l'enlèvement des messages</h2>

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente.
Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un
délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins
de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode
**GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite
chaque message à l'aide d'une boucle **foreach**. Il définit également le délai d'expiration de l'invisibilité
sur cinq minutes pour chaque message. Notez que le délai de 5 minutes
démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées
après l'appel de **GetMessages**, tous les messages n'ayant pas été supprimés
redeviennent visibles.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Traitez tous les messages en moins de 5 minutes, en supprimant chaque message après son traitement.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Obtention de la longueur de la file d'attente</span> Obtention de la longueur de la file d'attente</h2>

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode
**FetchAttributes** demande au service de File d'attente
d'extraire les attributs de la file d'attente, y compris le nombre de messages. La propriété **ApproximateMethodCount**
renvoie la dernière valeur extraite par la méthode
**FetchAttributes**, sans appeler le service de File d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Extrayez les attributs de file d'attente.
	queue.FetchAttributes();

    // Extrayez le nombre de messages approximatif mis en cache.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Affichez le nombre de messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">Suppression d'une file d'attente</span> Suppression d'une file d'attente</h2>

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode
**Delete** sur l'objet file d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la file d'attente.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Extrayez une référence pointant vers une file d'attente.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Supprimez la file d'attente.
    queue.Delete();

<h2><a name="next-steps"></a>Étapes suivantes</h2>

Maintenant que vous connaissez les bases du stockage de files d'attente, consultez les liens suivants
pour apprendre à réaliser des tâches de stockage plus complexes.

<ul>
<li>Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services de files d'attente :
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Référence de la bibliothèque de client de stockage pour .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355">Référence d'API REST</a></li>
  </ul>
</li>
<li>Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx">Stockage et accessibilité des données dans Windows Azure</a>.</li>
<li>Pour apprendre à utiliser Azure Storage dans les processus principaux relatifs à Sites Web Azure, consultez la page <a href="/fr-fr/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Prise en main du Kit de développement logiciel (SDK) Tâches web Azure</a>.</li>
<li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisez le <a href="/fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/">stockage de tables</a> pour stocker des données structurées.</li>
    <li>Utilisez le <a href="/fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/">stockage d'objets blob</a> pour stocker des données non structurées.</li>
    <li>Utilisez une <a href="/fr-fr/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li>
  </ul>
</li>
</ul>



  [Étapes suivantes]: #next-steps
  [Concepts]: #concepts
  [ Accès au stockage de files d'attente par programme]: #configure-access
  [ Création d'une file d'attente]: #create-queue
  [ Insertion d'un message dans une file d'attente]: #insert-message
  [ Lecture furtive du message suivant]: #peek-message
  [ Modification du contenu d'un message en file d'attente]: #change-contents
  [ Enlèvement du message suivant de la file d'attente]: #get-message
  [ Obtention de la longueur de la file d'attente]: #get-queue-length
  [ Suppression d'une file d'attente]: #delete-queue
  [CloudStorageAccount]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
