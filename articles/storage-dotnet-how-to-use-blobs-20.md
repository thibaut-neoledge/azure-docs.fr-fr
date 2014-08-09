<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Microsoft Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use the Windows Azure blob service to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use the Windows Azure Blob Storage Service in .NET" authors="tamram" />

# Utilisation du stockage d'objets blob à partir de .NET

Ce guide explique le déroulement des scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Azure. Les exemples ont été écrits en C# et utilisent la bibliothèque du client de stockage Azure pour .NET. Les scénarios traités incluent le **téléchargement
(vers une cible)**, la **création de listes**, le **téléchargement (à
partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#next-steps).

## Sommaire

* [Présentation du stockage d'objets blob](#what-is)
* [Concepts](#concepts)
* [Création d'un compte de stockage Azure](#create-account)
* [Configuration d'une chaîne de connexion de stockage](#setup-connection-string)
* [Accès au stockage d'objets blob par programme](#configure-access)
* [Création d'un conteneur](#create-container)
* [Téléchargement d'un objet blob dans un conteneur](#upload-blob)
* [Création d'une liste d'objets blob dans un conteneur](#list-blob)
* [Téléchargement d'objets blob](#download-blobs)
* [Suppression d'objets blob](#delete-blobs)
* [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span  class="short-header">Création d'un compte</span>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span  class="short-header">Configuration d'une chaîne de connexion</span>Configuration d'une chaîne de connexion de stockage

La bibliothèque du client de stockage Azure pour .NET prend en charge l'utilisation d'une chaîne de connexion de stockage pour la configuration de points de terminaison et d'informations d'identification permettant d'accéder aux services de stockage. Vous pouvez placer votre chaîne de connexion de stockage dans un fichier de configuration, au lieu de la coder en dur dans le code :

* Lorsque vous utilisez des services cloud Azure, il est recommandé de
  stocker votre chaîne de connexion en utilisant le système de
  configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
* Lorsque vous utilisez des sites Web Azure, des machines virtuelles
  Azure ou que vous développez des applications .NET conçues pour être
  exécutées en dehors d'Azure, il est recommandé de stocker votre chaîne
  de connexion en utilisant le système de configuration .NET (par
  exemple, le fichier `web.config` ou `app.config`).

La récupération de votre chaîne de connexion est présentée plus loin dans ce guide.

### Configuration de votre chaîne de connexion lors de l'utilisation des services cloud

Le mécanisme de configuration de service est propre aux projets de services cloud Azure et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier
    **Roles** de votre projet de déploiement Azure, cliquez avec le
    bouton droit sur votre rôle Web ou votre rôle de travail, puis
    cliquez sur **Properties**.  
     ![Sélectionner les propriétés sur un
    rôle de service cloud dans Visual
    Studio](./media/storage-dotnet-how-to-use-blobs-20/blob5.png)

2.  Cliquez sur l'onglet **Settings**, puis sur le bouton **Add Setting**.  
     ![Ajouter un paramètre de service cloud dans Visual
    Studio](./media/storage-dotnet-how-to-use-blobs-20/blob6.png)
    
    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la
    grille des paramètres.

3.  Dans le menu déroulant **Type** de l'entrée **Setting1**, sélectionnez **Connection String**.  
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Cliquez sur le bouton **...** situé à l'extrême droite de l'entrée
    **Setting1**. La boîte de dialogue **Storage Account Connection
    String** s'ouvre.

5.  Indiquez si vous voulez cibler l'émulateur de stockage (simulation
    de stockage Windows Azure sur votre machine locale) ou un compte de
    stockage dans le cloud. Le code de ce guide fonctionne dans les deux
    cas. Si vous voulez stocker des données d'objet blob dans le compte
    de stockage créé précédemment dans Azure, entrez la valeur **Primary
    Access Key** copiée lors de l'étape précédente de ce didacticiel.   
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Remplacez la valeur **Setting1** de l'entrée **Name** par un nom
    plus convivial tel que **ChaineConnexionStockage**. Le référencement
    de la chaîne de connexion dans le code sera effectué plus loin dans
    ce guide.  
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Configuration de votre chaîne de connexion en utilisant la configuration .NET

Si vous développez une application qui n'est pas un service cloud Azure (voir la section précédente), il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config` ou `app.config`). Ceci inclut les sites Web Azure ou les machines virtuelles Azure, ainsi que les applications conçues pour être exécutées hors de Azure. Stockez la chaîne de connexion en utilisant l'élément `<a ppSettings>` comme suit :

    <configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 Pour plus d'informations sur les chaînes de connexion de stockage, consultez la page [Configuration des chaînes de connexion][1].

 Vous pouvez à présent effectuer les tâches présentées dans ce guide.

## <a name="configure-access"> </a><span  class="short-header">Accès par programme</span>Accès au stockage d'objets blob par programme

### Obtention de l'assembly

 Vous pouvez utiliser NuGet pour obtenir l'assembly `Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit sur votre projet dans l'**Explorateur de solutions**, puis sélectionnez **Manage NuGet Packages**. Effectuez une recherche en ligne sur << WindowsAzure.Storage >>, puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

 `Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable dans le [Centre de développement .NET][2]. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk -version>\ref\`.

### Déclarations d'espace de noms

 Ajoutez les déclarations d'espace de noms suivantes en haut de chaque
fichier C# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

 Assurez-vous de bien référencer l'assembly `Microsoft.WindowsAzure.Storage.dll`.

### Récupération de votre chaîne de connexion

 Vous pouvez utiliser le type **CloudStorageAccount** pour représenter vos informations de compte de stockage. Si vous utilisez un modèle de projet Windows Azure et/ou qu'une référence pointe vers Microsoft.WindowsAzure.CloudConfigurationManager, vous pouvez utiliser le type **CloudConfigurationManager** pour récupérer votre chaîne de connexion de stockage, ainsi que vos informations de compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

 Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion. Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["ChaineConnexionStockage"].ConnectionString);

 Un type **CloudBlobClient** vous permet de récupérer des objets représentant des conteneurs et des objets blob stockés dans le serveur de stockage d'objets blob. Le code suivant crée un objet **CloudBlobClient** en utilisant l'objet de compte de stockage récupéré ci-dessus :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### Dépendances ODataLib

 Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib sont [OData][3], [Edm][4] et [Spatial][5].

## <a name="create-container"> </a><span  class="short-header">Création d'un conteneur</span>Création d'un conteneur

 Tous les objets blob de stockage résident dans un conteneur. Vous pouvez utiliser un objet **CloudBlobClient** pour obtenir une référence pointant vers le conteneur que vous voulez utiliser. Si le conteneur n'existe pas, vous pouvez le créer :

    // Récupérez un compte de stockage à partir d’une chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d’objet blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Créez le conteneur s’il n’existe pas déjà.
    container.CreateIfNotExists();

 Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =  

 BlobContainerPublicAccessType.Blob }); Tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais seuls ceux possédant la clé d'accès adéquate peuvent les modifier ou les supprimer.

 <h2> <a name="upload-blob"> </a><span  class="short-header">Téléchargement dans un conteneur</span>Téléchargement d'un objet blob dans un conteneur</h2>


 Le service de stockage d'objets blob Azure prend en charge les objets blob de blocs et de page. Dans la plupart des cas, il est recommandé d'utiliser le type d'objet blob de blocs.

 Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob de blocs. Lorsque vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream**. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L'exemple suivant illustre le téléchargement d'un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

    // Récupérez un compte de stockage à partir d’une chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d’objet blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
    
    // Créez ou remplacez l’objet blob "myblob" avec du contenu provenant d’un fichier local.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span  class="short-header">Création d'une liste d'objets blob dans un conteneur</span>Création d'une liste d'objets blob dans un conteneur

 Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **ListBlobs** du conteneur pour récupérer les objets blob et/ou les répertoires qu'il contient. Pour accéder aux nombreuses propriétés et méthodes d'une **IListBlobItem** renvoyée, vous devez l'appeler vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l'appel. Le code suivant illustre la récupération et la génération de l'URI de chaque élément du conteneur `photos` :

    // Récupérez un compte de stockage à partir d’une chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d’objet blob. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");
    
    // Créez une boucle autour des éléments du conteneur et obtenez la longueur et l’URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
    	if (item.GetType() == typeof(CloudBlockBlob))
    	{
    		CloudBlockBlob blob = (CloudBlockBlob)item;
    
    		Console.WriteLine("Objet blob de blocs de longueur {0} : {1}", blob.Properties.Length, blob.Uri);
                                        
    	}
    	else if (item.GetType() == typeof(CloudPageBlob))
    	{
    		CloudPageBlob pageBlob = (CloudPageBlob)item;
    
    		Console.WriteLine("Objet blob de page de longueur {0} : {1}", pageBlob.Properties.Length, pageBlob.Uri);
    
    	}
    	else if (item.GetType() == typeof(CloudBlobDirectory))
    	{
    		CloudBlobDirectory directory = (CloudBlobDirectory)item;
    		
    		Console.WriteLine("Directory: {0}", directory.Uri);
    	}
    }

 Comme indiqué précédemment, le service d'objet blob suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers. Par exemple, prenez l'ensemble d'objets blob de blocs suivant, situé dans un conteneur nommé `photos` :

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

 Lorsque vous appelez **ListBlobs** pour le conteneur << photos >> (comme dans l'exemple ci-dessus), la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlockBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Voici le résultat obtenu :

    Directory: https://<a ccountname>.blob.core.windows.net/photos/2010/
    Directory: https://<a ccountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 Vous pouvez également définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** sur **true**. Ceci aura pour résultat le renvoi de chaque objet blob en tant que **CloudBlockBlob**, indépendamment du répertoire. Voici ce que donnerait l'appel vers **ListBlobs** :

    // Créez une boucle autour des éléments du conteneur et obtenez la longueur et l’URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

 et voici les résultats :

    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<a ccountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 Pour plus d'informations, consultez [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a><span  class="short-header">Téléchargement d'objets blob</span>Téléchargement d'objets blob

 Pour télécharger des objets blob, commencez par récupérer une référence d'objet blob, puis appelez la méthode **DownloadToStream**. L'exemple suivant utilise la méthode **DownloadToStream** pour transférer les contenus d'objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local.

    // Récupérez un compte de stockage à partir d’une chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d’objet blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");
    
    // Enregistrez les contenus d’objets blob dans un fichier.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

 Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger les contenus d'un objet blob en tant que chaîne de texte.

    // Récupérez un compte de stockage à partir d’une chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d’objet blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");
    
    string text;
    using (var memoryStream = new MemoryStream())
    {
    	blockBlob2.DownloadToStream(memoryStream);
    	text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span  class="short-header">Suppression d'objets blob</span>Suppression d'objets blob

 Pour supprimer un objet blob, commencez par obtenir une référence d'objet blob, puis appelez la méthode **Delete** associée.

    // Récupérez un compte de stockage à partir d’une chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d’objet blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");
    
    // Supprimez l’objet blob.
    blockBlob.Delete();  

## <a name="next-steps"></a><span  class="short-header">Étapes suivantes</span>Étapes suivantes

 Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

 *  Pour plus d'informations sur les API disponibles, consultez la
  documentation de référence des services d'objets blob :
  *  [Référence de la bibliothèque de client de stockage pour .NET][6]
  *  [Référence d'API REST][7]
  

*  Pour plus d'informations sur les tâches avancées que vous pouvez
  effectuer avec le stockage Azure, consultez la page [Stockage et
  accessibilité des données dans Windows Azure][8].
*  Pour plus d'informations sur les autres options de stockage de données
  dans Azure, consultez d'autres guides de fonctionnalités.
  *  Utilisez le [stockage de
    table](/en-us/develop/net/how-to-guides/table-services/) pour stocker des données structurées.
  *  Utilisez une [base de données
    SQL](/en-us/develop/net/how-to-guides/sql-database/) pour stocker des données relationnelles.
  


 </appsettings>



[1]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/fr-fr/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn495001(v=azure.10).aspx
[7]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355
[8]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx