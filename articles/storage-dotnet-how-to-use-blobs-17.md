<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service (2.0)" pageTitle="How to use blob storage in .NET | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob .NET, Azure blob C#, Azure blob C#" description="Learn how to use the Azure blob service to upload,,download, list, and delete blob content. Samples are written in C#." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Azure Blob Storage Service in .NET" authors="" solutions="" manager="paulettm" editor="cgronlun" />

# Utilisation du service de stockage d'objets blob Azure dans .NET

<div class="dev-center-tutorial-selector">
<a href="/en-us/develop/net/how-to-guides/blob-storage-v17/" title="version 1.7" class="current">version 1.7</a>
<a href="/en-us/develop/net/how-to-guides/blob-storage/" title="version 2.0">version 2.0</a> 
</div>

Ce guide explique le déroulement des scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Azure. Les exemples sont écrits en C# et utilisent l'API .NET. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#next-steps).

<h2>Sommaire</h2>


* [Présentation du stockage d'objets blob](#what-is)
* [Concepts](#concepts)
* [Création d'un compte Azure Storage](#create-account)
* [Configuration d'une chaîne de connexion de stockage](#setup-connection-string)
* [ Accès au stockage d'objets blob par programme](#configure-access)
* [ Création d'un conteneur](#create-container)
* [ Téléchargement d'un objet blob dans un conteneur](#upload-blob)
* [ Création d'une liste d'objets blob dans un conteneur](#list-blob)
* [ Téléchargement d'objets blob](#download-blobs)
* [ Suppression d'objets blob](#delete-blobs)
* [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Création d'un compte</span>Création d'un compte de stockage Azure</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Configuration d'une chaîne de connexion</span>Configuration d'une chaîne de connexion de stockage</h2>


L'API de stockage Azure .NET prend en charge l'utilisation d'une chaîne de connexion de stockage pour la configuration de points de terminaison et d'informations d'identification permettant d'accéder aux services de stockage. Vous pouvez placer votre chaîne de connexion de stockage dans un fichier de configuration, au lieu de la coder en dur dans le code :

* Lorsque vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
* Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier `web.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, comme cela est présenté plus loin dans ce guide.

### Configuration de votre chaîne de connexion lors de l'utilisation de Cloud Services

Le mécanisme de configuration de service est propre aux projets Azure Cloud Services et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier **Roles** de votre projet de déploiement Azure, cliquez avec le bouton droit sur votre rôle Web ou votre rôle de travail, puis cliquez sur **Properties**.  
     ![Sélectionner les propriétés sur un rôle de service cloud dans Visual Studio](./media/storage-dotnet-how-to-use-blobs-17/blob5.png)

2.  Cliquez sur l'onglet **Settings**, puis sur le bouton **Add Setting**.  
     ![Ajouter un paramètre de service cloud dans Visual Studio](./media/storage-dotnet-how-to-use-blobs-17/blob6.png)
    
    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la grille des paramètres.

3.  Dans le menu déroulant **Type** de l'entrée **Setting1**, sélectionnez **Connection String**.  
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Cliquez sur le bouton **...** situé à l'extrême droite de l'entrée **Setting1**. La boîte de dialogue **Storage Account Connection String** s'ouvre.

5.  Indiquez si vous voulez cibler l'émulateur de stockage (simulation de stockage Windows Azure sur votre machine locale) ou un compte de stockage existant dans le cloud. Le code de ce guide fonctionne dans les deux cas. Si vous voulez stocker des données d'objet blob dans le compte de stockage créé précédemment dans Azure, entrez la valeur **Primary Access Key** copiée lors de l'étape précédente de ce didacticiel.   
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Remplacez la valeur **Setting1** de l'entrée **Name** par un nom plus convivial tel que **ChaineConnexionStockage**. Le référencement de la chaîne de connexion dans le code sera effectué plus loin dans ce guide.  
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Configuration de votre chaîne de connexion dans le cadre de l'utilisation de Sites Web Azure ou Azure Virtual Machines

Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config`). Stockez la chaîne de connexion en utilisant l'élément `<a ppSettings>` :

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

Pour plus d'informations sur les chaînes de connexion de stockage, consultez la page [Configuration des chaînes de connexion][1].

Vous pouvez à présent effectuer les tâches présentées dans ce guide.

<h2> <a name="configure-access"> </a><span  class="short-header">Accès par programme</span> Accès au stockage d'objets blob par programme</h2>


Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Vous pouvez utiliser les types **CloudStorageAccount** et **CloudConfigurationManager** pour récupérer votre chaîne de connexion de stockage, ainsi que vos informations de compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

Un type **CloudBlobClient** vous permet de récupérer des objets représentant des conteneurs et des objets blob stockés dans le serveur de stockage d'objets blob. Le code suivant crée un objet **CloudBlobClient** en utilisant l'objet de compte de stockage récupéré ci-dessus :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

<h2> <a name="create-container"> </a><span  class="short-header">Création d'un conteneur</span> Création d'un conteneur</h2>


Tous les objets blob de stockage résident dans un conteneur. Vous pouvez utiliser un objet **CloudBlobClient** pour obtenir une référence pointant vers le conteneur que vous voulez utiliser. Si le conteneur n'existe pas, vous pouvez le créer :

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d'objet blob 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Créez le conteneur s'il n'existe pas déjà
    container.CreateIfNotExist();

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =  

 BlobContainerPublicAccessType.Blob }); Tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais seuls ceux possédant la clé d'accès adéquate peuvent les modifier ou les supprimer.

<h2> <a name="upload-blob"> </a><span  class="short-header">Téléchargement dans un conteneur</span> Téléchargement d'un objet blob dans un conteneur</h2>


Pour télécharger un fichier vers un objet blob, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob. Lorsque vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream** sur la référence d'objet blob. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L'exemple de code ci-après illustre ce point en supposant que le conteneur existe.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Créez ou remplacez l'objet blob "myblob" avec du contenu provenant d'un fichier local
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

<h2> <a name="list-blob"> </a><span  class="short-header">Création d'une liste d'objets blob dans un conteneur</span> Création d'une liste d'objets blob dans un conteneur</h2>


Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **ListBlobs** du conteneur pour récupérer les objets blob qu'il contient. Le code suivant illustre la récupération et la génération de l'**URI** de chaque objet blob d'un conteneur :

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Créez une boucle autour des objets blob du conteneur et sortez l'URI dans chacun d'eux
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

Le service BLOB suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers. Par exemple, vous pouvez avoir un conteneur nommé << photos >>, dans lequel vous pouvez télécharger des objets blob nommés << rootphoto1 >>, << 2010/photo1 >>, << 2010/photo2 >> et << 2011/photo1 >>. Vous créez ainsi virtuellement les répertoires << 2010 >> et << 2011 >> dans le conteneur << photos >>. Lorsque vous appelez **ListBlobs** pour le conteneur << photos >>, la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Dans ce cas, les répertoires << 2010 >> et << 2011 >> et la photo << rootphoto1 >> sont renvoyés. Vous pouvez éventuellement transmettre une nouvelle classe **BlobRequestOptions** avec **UseFlatBlobListing** défini sur **true**. Cela permet de renvoyer chaque objet blob, indépendamment du répertoire. Pour plus d'informations, consultez [CloudBlobContainer.ListBlobs][].

<h2> <a name="download-blobs"> </a><span  class="short-header">Téléchargement d'objets blob</span> Téléchargement d'objets blob</h2>


Pour télécharger des objets blob, commencez par récupérer une référence d'objet blob. L'exemple suivant utilise la méthode **DownloadToStream** pour transférer les contenus d'objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local. Vous pouvez également appeler les méthodes **DownloadToFile**, **DownloadByteArray** ou **DownloadText** de l'objet blob.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Enregistrez le contenu des objets blob sur disque
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

<h2> <a name="delete-blobs"> </a><span  class="short-header">Suppression d'objets blob</span> Suppression d'objets blob</h2>


Pour supprimer un objet blob, commencez par obtenir une référence d'objet blob, puis appelez la méthode **Delete** associée.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Récupérez une référence pointant vers un objet blob nommé "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Supprimez l'objet blob
    blob.Delete(); 

<h2> <a name="next-steps"></a><span  class="short-header">Étapes suivantes</span>Étapes suivantes</h2>


Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.
*  Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services d'objets blob :
  *  [Référence de bibliothèque cliente .NET][2]
  *  [Référence d'API REST][3]
*  Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page [Stockage et accessibilité des données dans Azure][4].
*  Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  *  Utilisez le [stockage de table](/en-us/develop/net/how-to-guides/table-services/) pour stocker des données structurées.
  *  Utilisez une [base de données SQL](/en-us/develop/net/how-to-guides/sql-database/) pour stocker des données relationnelles.
  




[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx