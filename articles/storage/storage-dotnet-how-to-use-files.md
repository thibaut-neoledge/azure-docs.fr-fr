<properties
			pageTitle="Comment utiliser le stockage de fichiers Azure avec PowerShell et .NET | Microsoft Azure"
            description="Découvrez comment utiliser le stockage de fichiers Azure pour créer des partages de fichiers dans le cloud et gérer le contenu des fichiers. Le stockage de fichiers permet aux entreprises de déplacer vers Azure les applications qui utilisent des partages de fichiers SMB. Conservez les informations d’identification de votre compte de stockage pour la machine virtuelle afin de vous reconnecter au partage de fichiers au redémarrage."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="08/04/2015"
      ms.author="tamram" />

# Comment utiliser le stockage de fichiers Azure avec PowerShell et .NET

## Vue d'ensemble

Le service de fichiers Azure expose les partages de fichiers à l’aide du protocole SMB 2.1 standard. Les applications exécutées dans Azure peuvent désormais facilement partager des fichiers entre des machines virtuelles à l’aide d’API de système de fichiers standard et connues, comme ReadFile et WriteFile. En outre, les fichiers sont également accessibles en même temps via une interface REST, qui ouvre une variété de scénarios hybrides. Pour finir, Azure Files repose sur la même technologie que les services BLOB, de Table et de File d’attente, ce qui signifie qu’Azure Files est en mesure de tirer parti de la redondance géographique intégrée à notre plateforme de stockage Azure, de la durabilité, de l’évolutivité et de la disponibilité existantes.

## À propos de ce didacticiel

Ce didacticiel de prise en main présente les bases de l’utilisation du stockage de fichiers Microsoft Azure. Dans ce didacticiel, nous allons :

- utiliser Azure PowerShell pour créer un partage de fichiers Azure, ajouter un répertoire, charger un fichier local vers le partage et afficher la liste des fichiers du répertoire ;
- monter le partage de fichiers à partir d’une machine virtuelle Azure, comme tout partage SMB ;
- utiliser la bibliothèque cliente de stockage Azure pour .NET pour accéder au partage de fichiers à partir d’une application locale. Créez une application console et exécutez ces actions avec le partage de fichiers :
	- Écrire le contenu d’un fichier du partage dans la fenêtre de la console
	- Définir le quota (taille maximale) pour le partage de fichiers
	- Créer une signature d’accès partagé pour un fichier qui utilise une stratégie d’accès partagé définie sur le partage
	- Copier un fichier dans un autre fichier au sein du même compte de stockage
	- Copier un fichier dans un objet blob au sein du même compte de stockage

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Création d’un compte Azure Storage

Un stockage de fichiers Azure est actuellement disponible en version préliminaire. Pour demander un accès à cette version, allez sur la [page de la version préliminaire de Microsoft Azure](/services/preview/) et demandez un accès aux **fichiers Azure**. Lorsque votre demande est acceptée, vous recevez une notification indiquant que vous pouvez accéder à la version préliminaire du stockage de fichiers. Vous pouvez alors créer un compte de stockage pour accéder au stockage de fichiers.

> [AZURE.NOTE]Le stockage de fichiers n’est actuellement disponible que pour les nouveaux comptes de stockage. Lorsque votre abonnement peut accéder au stockage de fichiers, créez un compte de stockage à utiliser avec ce guide.
>
> Le stockage de fichiers Azure ne prend pas actuellement en charge les signatures d’accès partagé.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Utilisation de PowerShell pour créer un partage de fichiers

Ensuite, nous utiliserons Azure PowerShell pour créer un partage de fichiers. Une fois le partage de fichiers créé, vous pouvez le monter à partir de n’importe quel système de fichiers prenant en charge SMB 2.1.

### Installation des applets de commande PowerShell pour Azure Storage

Pour vous préparer à utiliser PowerShell, téléchargez et installez les applets de commande PowerShell Azure. Consultez la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md) pour des instructions sur l’installation et le point d’installation.

> [AZURE.NOTE]Les applets de commande PowerShell du service de fichiers ne sont disponibles que dans le dernier module Azure PowerShell, version 0.8.5 et ultérieure. Il est recommandé de télécharger et d’installer le dernier module Azure PowerShell ou d’effectuer une mise à niveau vers celui-ci.

Ouvrez une fenêtre Azure PowerShell en cliquant sur **Démarrer** et en tapant **Azure PowerShell**. La fenêtre Azure PowerShell charge le module Azure PowerShell.

### Création d'un contexte pour votre compte de stockage et votre clé

Créez maintenant le contexte du compte de stockage. Celui-ci encapsule le nom et la clé du compte de stockage. Pour obtenir des instructions sur la copie de votre clé de compte à partir du portail Azure, consultez [Afficher, copier et régénérer des clés d’accès de stockage](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Remplacez `storage-account-name` et `storage-account-key` par vos nom et clé de compte de stockage dans l’exemple suivant :

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Création d’un partage de fichiers

Créez ensuite le partage de fichiers, appelé `logs` dans cet exemple :

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

Vous disposez désormais d’un partage de fichier dans le stockage de fichiers. Nous allons maintenant ajouter un répertoire et un fichier.

> [AZURE.IMPORTANT]Le nom de votre partage de fichiers doit être en minuscules. Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Création d’un répertoire dans le partage de fichiers

Créez un répertoire dans le partage. Dans l’exemple suivant, le répertoire est appelé `CustomLogs` :

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Chargement d’un fichier local vers le répertoire

Chargez un fichier local vers le répertoire. L’exemple suivant charge un fichier à partir de `C:\temp\Log1.txt`. Modifiez le chemin d’accès du fichier de façon à ce qu’il désigne un fichier valide sur votre ordinateur local :

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Affichage de la liste des fichiers du répertoire

Pour voir le fichier dans le répertoire, vous pouvez afficher la liste des fichiers du répertoire. Cette commande affiche également la liste des sous-répertoires. Toutefois, comme il n’y en a pas dans cet exemple, seul le fichier est affiché.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Montage du partage à partir d’une machine virtuelle Azure exécutant Windows

Pour illustrer comment monter un partage de fichiers Azure, nous allons créer une machine virtuelle Azure exécutant Windows, à laquelle nous accéderons à distance pour monter le partage.

1. Tout d’abord, créez une machine virtuelle Azure en suivant les instructions de la rubrique [Création d’une machine virtuelle exécutant Windows Server](../virtual-machines-windows-tutorial.md).
2. Ensuite, connectez-vous à distance à la machine virtuelle en suivant les instructions de la rubrique [Connexion à une machine virtuelle exécutant Windows Server](../virtual-machines-log-on-windows-server.md).
3. Ouvrez une fenêtre PowerShell sur la machine virtuelle.

### Conservation des informations d'identification de votre compte de stockage pour la machine virtuelle

Avant de monter le partage de fichiers, conservez les informations d'identification de votre compte de stockage sur la machine virtuelle. Cette étape permet à Windows de se reconnecter automatiquement au partage de fichiers lorsque la machine virtuelle redémarre. Pour conserver vos informations d’identification, exécutez la commande `cmdkey` à partir de la fenêtre PowerShell de la machine virtuelle. Remplacez `<storage-account-name>` par le nom de votre compte de stockage et `<storage-account-key>` par la clé de votre compte de stockage :

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows se reconnectera à votre partage de fichiers lorsque la machine virtuelle redémarrera. Il est possible de vérifier que le partage est bien reconnecté en exécutant la commande `net use` dans une fenêtre PowerShell.

### Montage du partage de fichiers à l’aide des informations d’identification conservées

Une fois une connexion à distance établie avec la machine virtuelle, vous pouvez exécuter la commande `net use` pour monter le partage de fichiers à l’aide de la syntaxe suivante. Remplacez `<storage-account-name>` par le nom de votre compte de stockage et `<share-name>` par le nom du partage de stockage de fichiers.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

Comme vous avez conservé les informations d’identification de votre compte de stockage à l’étape précédente, vous n’avez pas besoin de les préciser avec la commande `net use`. Si vous n’avez pas conservé vos informations d’identification, incluez-les en les transmettant comme paramètres dans la commande `net use`, comme illustré dans cet exemple :

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Vous pouvez maintenant utiliser le partage de stockage de fichiers à partir de la machine virtuelle, comme vous le feriez à partir de n’importe quel autre lecteur. Vous pouvez émettre des commandes de fichier standard à partir de l’invite de commandes ou afficher le partage monté et son contenu à partir de l’Explorateur de fichiers. Vous pouvez également exécuter du code au sein de la machine virtuelle qui accède au partage de fichiers à l'aide d'API d'E/S de fichiers Windows standard, telles que celles fournies par les [espaces de noms System.IO](http://msdn.microsoft.com/library/gg145019.aspx) dans .NET Framework.

Vous pouvez également monter le partage de fichiers à partir d’un rôle exécuté dans un service cloud Azure en vous connectant à distance à ce rôle.

## Création d’une application locale pour utiliser le stockage de fichiers

Vous pouvez monter le partage de fichiers à partir d’une machine virtuelle ou d’un service cloud exécuté dans Azure, comme illustré ci-dessus. Toutefois, vous ne pouvez pas monter le partage de fichiers à partir d’une application locale. Pour accéder aux données de partage à partir d’une application locale, vous devez utiliser l’API de stockage de fichiers. Cet exemple montre comment utiliser un partage de fichiers avec la [bibliothèque cliente de stockage .NET Azure](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409).

Afin de montrer comment utiliser l’API à partir d’une application locale, nous allons créer une application console simple à exécuter sur le Bureau.

### Création de l’application console et obtention de l’assembly

Pour créer une application console dans Visual Studio et installer le package NuGet pour Azure Storage :

1. Dans Visual Studio, choisissez **Fichier** -> **Nouveau projet**, puis **Windows** -> **Application console** dans la liste de modèles Visual C#.
2. Nommez l'application console, puis cliquez sur **OK**.
3. Une fois le projet créé, cliquez dessus avec le bouton droit dans l’Explorateur de solutions et choisissez **Gérer les packages NuGet**. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

### Enregistrement des informations d’identification de votre compte de stockage dans le fichier app.config

Enregistrez ensuite vos informations d’identification dans le fichier app.config du projet. Modifiez le fichier app.config de façon à ce qu’il apparaisse de la même façon que dans l’exemple ci-après, en remplaçant `myaccount` par le nom de votre compte de stockage et `mykey` par la clé de votre compte de stockage :

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]La dernière version de l’émulateur de stockage Azure ne prend pas en charge le stockage de fichiers. Votre chaîne de connexion doit cibler un compte de stockage Azure dans le cloud avec un accès à la version préliminaire des fichiers.


### Ajout de déclarations d'espaces de noms

Ouvrez le fichier program.cs à partir de l'Explorateur de solutions et ajoutez les déclarations d'espaces de noms en haut du fichier :

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### Extraction de votre chaîne de connexion par programme

Vous pouvez extraire vos informations d’identification enregistrées dans le fichier app.config en utilisant la classe `Microsoft.WindowsAzure.CloudConfigurationManager` ou `System.Configuration.ConfigurationManager `. Le package Microsoft Azure Configuration Manager, qui inclut la classe `Microsoft.WindowsAzure.CloudConfigurationManager`, est disponible sur [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager).

L’exemple présenté ici montre comment récupérer vos informations d’identification à l’aide de la classe `CloudConfigurationManager` et les encapsuler avec la classe `CloudStorageAccount`. Ajoutez le code suivant à la méthode `Main()` dans program.cs :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### Accès au partage de fichiers par programmation

Ajoutez ensuite le code suivant à la méthode `Main()` après le code affiché ci-dessus afin d’extraire la chaîne de connexion. Ce code obtient une référence vers le fichier créé plus tôt et renvoie son contenu dans la fenêtre de console.

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        //Ensure that the file exists.
	        if (file.Exists())
	        {
	            //Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Exécutez l’application console pour voir le résultat.

## Définition de la taille maximale d’un partage de fichiers

Depuis la version 5.x de la bibliothèque cliente de stockage Azure, vous pouvez définir le quota (ou la taille maximale) pour un partage, en gigaoctets. En définissant le quota pour un partage, vous pouvez limiter la taille totale des fichiers stockés sur ce partage.

Si la taille totale des fichiers sur le partage dépasse le quota défini sur celui-ci, les clients ne peuvent pas augmenter la taille des fichiers existants ou créer des fichiers, sauf s’ils sont vides.

L’exemple ci-dessous montre comment définir le quota pour un partage de fichiers existant.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
		//Specify the maximum size of the share, in GB.
	    share.Properties.Quota = 100;
	    share.SetProperties();
	}

Pour obtenir la valeur d’un quota existant pour le partage, appelez la méthode **FetchAttributes()** pour récupérer les propriétés du partage.

## Génération d’une signature d’accès partagé pour un fichier ou partage de fichiers

Depuis la version 5.x de la bibliothèque cliente de stockage Azure, vous pouvez générer une signature d’accès partagé (SAP) pour un partage de fichiers ou un fichier individuel. Vous pouvez également créer une stratégie d’accès partagé sur un partage de fichiers pour gérer les signatures d’accès partagé. Créer une stratégie d’accès partagé est recommandé, car cette opération permet de révoquer la SAP si elle doit être compromise.

L’exemple ci-dessous crée une stratégie d’accès partagé sur un partage, puis utilise cette stratégie pour fournir les contraintes pour une SAP sur un fichier dans le partage.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        //Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        //Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        //Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        //Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        //Create a new CloudFile object from the SAS, and write some text to the file. 
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

Pour plus d’informations sur la création et l’utilisation de signatures d’accès partagé, consultez [Signatures d’accès partagé : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md) et [Créer et utiliser une SAP avec le service BLOB](storage-dotnet-shared-access-signature-part-2.md).

## Copie des fichiers

Depuis la version 5.x de la bibliothèque cliente de stockage Azure, vous pouvez copier un fichier dans un autre, un fichier dans un objet blob ou un objet blob dans un fichier. Ci-dessous, nous montrons comment effectuer ces opérations de copie par programmation.

Vous pouvez également utiliser AzCopy pour copier un fichier dans un autre ou pour copier un objet blob dans un fichier ou vice versa. Pour plus d’informations sur la copie de fichiers avec AzCopy, consultez [Comment utiliser AzCopy avec Microsoft Azure Storage](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only).

> [AZURE.NOTE]Si vous copiez un objet blob dans un fichier ou un fichier dans un objet blob, vous devez utiliser une signature d’accès partagé (SAP) pour authentifier l’objet source, même si vous effectuez la copie dans le même compte de stockage.

### Copie d’un fichier dans un autre

L’exemple suivant copie un fichier dans un autre au sein du même partage. Étant donné que cette opération de copie a lieu entre des fichiers du même compte de stockage, vous pouvez utiliser l’authentification Clé partagée pour l’effectuer.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
        //Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        //Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        //Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            //Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            //Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                //Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                //Start the copy operation.
                destFile.StartCopy(sourceFile);

                //Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### Copie d’un fichier dans un objet blob

L’exemple ci-dessous crée un fichier et le copie dans un objet blob au sein du même compte de stockage. L’exemple crée une SAP pour le fichier source, que le service utilise pour authentifier l’accès au fichier source pendant l’opération de copie.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    //Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    //Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    //Create a SAS for the file that's valid for 24 hours.
    //Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    //to authenticate access to the source object, even if you are copying within the same 
    //storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        //Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    //Construct the URI to the source file, including the SAS token. 
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    //Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    //Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

Vous pouvez copier un objet blob dans un fichier de la même façon. Si l’objet source est un objet blob, créez une SAP pour authentifier l’accès à cet objet blob pendant l’opération de copie.

## Utilisation du stockage de fichiers avec Linux

Pour créer et gérer un partage de fichiers à partir de Linux, utilisez l’interface de ligne de commande Azure. Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure avec le stockage de fichiers, consultez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](storage-azure-cli.md#create-and-manage-file-shares).

Vous pouvez monter un partage de fichiers Azure à partir d’une machine virtuelle exécutant Linux. Quand vous créez votre machine virtuelle Azure, vous pouvez spécifier une image Linux qui prend en charge SMB 2.1 à partir de la galerie d’images Azure, telle que la dernière version d’Ubuntu. Toutefois, une distribution Linux prenant en charge SMB 2.1 peut monter le partage de fichiers Azure.

Pour plus d’informations sur le montage d’un partage de fichiers Azure sous Linux, consultez la vidéo relative au [stockage partagé sous Linux via l’aperçu de fichiers Azure - Partie 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) sur Channel 9.

## Étapes suivantes

Pour plus d’informations sur le stockage de fichiers Azure, consultez ces liens.

### Didacticiels et référence

- [Référence de la bibliothèque cliente de stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Utiliser AzCopy avec Microsoft Azure Storage](storage-use-azcopy.md)
- [Utilisation d'Azure PowerShell avec Azure Storage](storage-powershell-guide-full.md)
- [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](storage-azure-cli.md)

### Billets de blog :

- [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Conservation des connexions vers les fichiers Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
 

<!---HONumber=August15_HO6-->