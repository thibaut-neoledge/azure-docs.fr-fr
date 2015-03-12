<properties 
	pageTitle="Utilisation du stockage de fichiers Azure | Microsoft Azure" 
	description="Découvrez comment utiliser le stockage de fichiers Microsoft Azure pour créer des partages de fichiers et gérer le contenu des fichiers. Les exemples sont écrits en PowerShell et C#." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# Utilisation du stockage de fichiers Azure

Dans ce guide de prise en main, nous découvrirons les bases de l'utilisation du stockage de fichiers Microsoft Azure. Nous utiliserons d'abord PowerShell pour créer un partage de fichiers Azure, ajouter un répertoire, télécharger un fichier local vers le partage et afficher la liste des fichiers du répertoire. Ensuite, nous verrons comment monter le partage de fichiers à partir d'une machine virtuelle Azure, comme tout partage SMB.

Pour les utilisateurs qui souhaiteraient accéder aux fichiers d'un partage à partir d'une application locale, d'une machine virtuelle Azure ou d'un service cloud, nous verrons comment utiliser la bibliothèque cliente de stockage .NET Azure afin de travailler avec le partage de fichiers à partir d'une application de bureau.

> [AZURE.NOTE] Pour exécuter les exemples de code .NET de ce guide, la bibliothèque cliente de stockage .NET Azure 4.x ou une version ultérieure est nécessaire. La bibliothèque cliente de stockage est disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).


##Sommaire

-   [Présentation du stockage de fichiers][]
-   [Concepts du stockage de fichiers][]
-   [Création d'un compte de stockage Azure][]
-   [Utilisation de PowerShell pour créer un partage de fichiers][]
-	[Montage du partage à partir d'une machine virtuelle Azure][]
-   [Création d'une application locale pour accéder au stockage de fichiers][]
-   [Étapes suivantes][]


##<a name="what-is-file-storage"></a>Présentation du stockage de fichiers

Le stockage de fichiers propose un stockage partagé pour les applications utilisant le protocole SMB 2.1. Les machines virtuelles et les services cloud Microsoft Azure peuvent partager des données de fichiers entre plusieurs composants d'application grâce à des partages montés. Les applications locales peuvent accéder aux données de fichiers d'un partage via l'API de stockage de fichiers.

Les applications exécutées dans les machines virtuelles ou services cloud Azure peuvent monter un partage de stockage de fichiers pour accéder aux données de fichiers, tout comme une application de bureau monterait un partage SMB classique. Un nombre indéfini de machines virtuelles ou de rôles Azure peuvent monter le partage de stockage de fichiers et y accéder simultanément.

Un partage de stockage de fichiers étant un partage de fichiers SMB 2.1 standard, les applications exécutées dans Azure peuvent accéder aux données du partage grâce à des API d'E/S de fichiers. Les développeurs peuvent ainsi tirer profit de leur code et compétences actuels pour migrer les applications existantes. Les professionnels de l'informatique peuvent utiliser des applets de commande PowerShell pour créer, monter et gérer les partages de stockage de fichiers dans le cadre de l'administration des applications Azure. Vous trouverez dans ce guide des exemples de ces deux méthodes.

Voici quelques utilisations courantes du partage de fichiers :

- Migration d'applications locales reposant sur des partages de fichiers à exécuter sur des machines virtuelles ou services cloud Azure, sans réécriture coûteuse
- Stockage de paramètres d'application partagés, comme dans les fichiers de configuration
- Stockage de données de diagnostic, comme des journaux, des indicateurs de performances et des vidages sur incident dans un emplacement partagé 
- Stockage d'outils et d'utilitaires nécessaires au développement ou à l'administration de machines virtuelles ou de services cloud Azure

##<a name="file-storage-concepts"></a>Concepts du stockage de fichiers

Le stockage de fichiers est composé des éléments suivants :

![files-concepts][files-concepts]


-   **Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Pour plus d'informations sur la capacité et les objectifs de performance du compte de stockage, consultez la page [Objectifs d'extensibilité et de performances d'Azure Storage](http://msdn.microsoft.com/library/dn249410.aspx).

-   **Partage :** un partage de stockage de fichiers est un partage de fichiers SMB 2.1 dans Azure. Tous les répertoires et fichiers doivent être créés dans un partage parent. Un compte peut contenir un nombre illimité de partages et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

-   **Répertoire :** hiérarchie optionnelle de répertoires.

-	**Fichier :** un fichier dans le partage. La taille maximale de fichier est de 1 To.

-   **Format d'URL :** les fichiers sont adressables à l'aide du format d'URL suivant : https://`<storageaccount>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`
L'URL de l'exemple suivant peut être utilisée pour traiter l'un des fichiers dans le schéma ci-dessus :
`http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



Pour plus d'informations sur la façon de nommer les partages, les répertoires et les fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](http://msdn.microsoft.com/library/azure/dn167011.aspx).

##<a name="create-account"></a>Création d'un compte Azure Storage

Un stockage de fichiers Azure est actuellement disponible en version préliminaire. Pour demander un accès à cette version, allez sur la page de la [version préliminaire de Microsoft Azure](/fr-FR/services/preview/) et demandez un accès aux **fichiers Azure**. Lorsque votre demande est acceptée, vous recevez une notification indiquant que vous pouvez accéder à la version préliminaire du stockage de fichiers. Vous pouvez alors créer un compte de stockage pour accéder au stockage de fichiers.

> [AZURE.NOTE] Le stockage de fichiers n'est actuellement disponible que pour les nouveaux comptes de stockage. Lorsque votre abonnement peut accéder au stockage de fichiers, créez un compte de stockage à utiliser avec ce guide.

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="use-cmdlets"></a>Utilisation de PowerShell pour créer un partage de fichiers

###Installation des applets de commande pour le stockage Azure

Pour vous préparer à utiliser PowerShell, téléchargez et installez les applets de commande PowerShell Azure. Consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-FR/documentation/articles/install-configure-powershell/) pour des instructions sur l'installation et le point d'installation.

> [AZURE.NOTE] Les applets de commande PowerShell du service de fichiers ne sont disponibles que dans le dernier module Azure PowerShell, version 0.8.5 et ultérieure. Il est recommandé de télécharger et d'installer le dernier module Azure PowerShell ou d'effectuer une mise à niveau vers celui-ci.

Ouvrez une fenêtre Azure PowerShell en cliquant sur **Démarrer** et en tapant **Windows Azure PowerShell**. La fenêtre Azure PowerShell charge le module Azure PowerShell.

###Création d'un contexte pour votre compte de stockage et votre clé

Créez maintenant le contexte du compte de stockage. Celui-ci encapsule le nom et la clé du compte. Remplacez `account-name` et `account-key` par vos nom et clé de compte dans l'exemple suivant :

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key
    
###Création d'un partage de fichiers

Créez ensuite le partage de fichiers, appelé `sampleshare` dans cet exemple :

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

Vous disposez désormais d'un partage de fichier dans le stockage de fichiers. Nous allons maintenant ajouter un répertoire et un fichier.

###Création d'un répertoire dans le partage de fichiers

Créez un répertoire dans le partage. Dans l'exemple suivant, le répertoire est appelé `sampledir` :

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

###Téléchargement d'un fichier local vers le répertoire

Téléchargez un fichier local vers le répertoire. L'exemple suivant télécharge un fichier à partir de `C:\temp\samplefile.txt`. Modifiez le chemin d'accès du fichier de façon à ce qu'il désigne un fichier valide sur votre ordinateur local : 
    
    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Chemin d'accès sampledir

###Affichage de la liste des fichiers du répertoire

Pour voir le fichier dans le répertoire, vous pouvez afficher la liste des fichiers du répertoire. Cette commande affiche également la liste des sous-répertoires. Toutefois, comme il n'y en a pas dans cet exemple, seul le fichier est affiché.  

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

##<a name="mount-share"></a>Montage du partage à partir d'une machine virtuelle Azure

Pour illustrer comment monter un partage de fichiers Azure, nous allons créer une machine virtuelle Azure, à laquelle nous accéderons à distance pour monter le partage. 

1. Tout d'abord, créez une machine virtuelle Azure en suivant les instructions de la rubrique [Création d'une machine virtuelle exécutant Windows Server](/fr-FR/documentation/articles/virtual-machines-windows-tutorial/).
2. Ensuite, connectez-vous à distance à la machine virtuelle en suivant les instructions de la rubrique [Connexion à une machine virtuelle exécutant Windows Server](/fr-FR/documentation/articles/virtual-machines-log-on-windows-server/).
3. Ouvrez une fenêtre PowerShell sur la machine virtuelle. 

###Conservation des informations d'identification de votre compte de stockage pour la machine virtuelle

Avant de monter le partage de fichiers, conservez les informations d'identification de votre compte de stockage sur la machine virtuelle. Cette étape permet à Windows de se reconnecter automatiquement au partage de fichiers lorsque la machine virtuelle redémarre. Pour conserver vos informations d'identification, exécutez la commande `cmdkey` à partir de la fenêtre PowerShell de la machine virtuelle. Remplacez `<storage-account>` par le nom de votre compte de stockage et `<account-key>` par la clé du compte de stockage :

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

Windows se reconnectera à votre partage de fichiers lorsque la machine virtuelle redémarrera. Il est possible de vérifier que le partage est bien reconnecté en exécutant la commande  `net use` dans une fenêtre PowerShell.

###Montage du partage de fichiers à l'aide des informations d'identification conservées

Une fois une connexion à distance établie avec la machine virtuelle, vous pouvez exécuter la commande `net use` pour monter le partage de fichiers à l'aide de la syntaxe suivante. Remplacez `<storage-account>` par le nom de votre compte de stockage et `<share-name>` par le nom du partage de stockage de fichiers.

	net use z: \\<storage-account>.file.core.windows.net\<share-name>

> [AZURE.NOTE] Comme vous avez conservé les informations d'identification de votre compte de stockage à l'étape précédente, vous n'avez pas besoin de les préciser avec la commande `net use`. Si vous n'avez pas conservé vos informations d'identification, incluez-les en les transmettant comme paramètres dans la commande `net use`. Remplacez `<storage-account>` par le nom de votre compte de stockage, `<share-name>` par le nom du partage de stockage de fichiers et `<account-key>` par la clé du compte de stockage :
	   
	net use z: \\<storage-account>.file.core.windows.net\<share-name> /u:<storage-account> <account-key>

Vous pouvez maintenant utiliser le partage de stockage de fichiers à partir de la machine virtuelle, comme vous le feriez à partir de n'importe quel autre lecteur. Vous pouvez émettre des commandes de fichier standard à partir de l'invite de commandes ou afficher le partage monté et son contenu à partir de l'Explorateur de fichiers. VVous pouvez également exécuter du code au sein de la machine virtuelle qui accède au partage de fichiers à l'aide d'API d'E/S de fichiers Windows standard, telles que celles fournies par les [espaces de noms System.IO](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx)dans .NET Framework. 

Vous pouvez également monter le partage de fichiers à partir d'un rôle exécuté dans un service cloud Azure en vous connectant à distance à ce rôle.

##<a name="create-console-app"></a>Création d'une application locale pour utiliser le stockage de fichiers

Vous pouvez monter un partage de stockage de fichiers à partir d'une machine virtuelle ou d'un service cloud exécuté dans Azure, comme illustré ci-dessus. Toutefois, vous ne pouvez pas monter un partage de stockage de fichiers à partir d'une application locale. Pour accéder aux données de partage à partir d'une application locale, vous devez utiliser l'API de stockage de fichiers. Cet exemple montre comment utiliser un partage de fichiers avec la [bibliothèque cliente de stockage .NET Azure](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409). 

Afin de montrer comment utiliser l'API à partir d'une application locale, nous allons créer une application console simple à exécuter sur le Bureau.

###Création de l'application console et obtention de l'assembly

Pour créer une application console dans Visual Studio et installer le package NuGet pour Azure Storage :

1. Dans Visual Studio, sélectionnez **Fichier** -> **Nouveau Projet**, puis sélectionnez **Windows** -> **Application console** dans la liste de modèles Visual C#.
2. Nommez l'application console, puis cliquez sur **OK**.
3. Une fois le projet créé, cliquez dessus avec le bouton droit dans l'Explorateur de solutions et choisissez **Gérer les packages NuGet**. Effectuez une recherche en ligne sur " WindowsAzure.Storage ", puis cliquez sur **Installer** pour lancer l'installation du package Azure Storage et de ses dépendances.

###Enregistrement des informations d'identification de votre compte de stockage dans le fichier app.config

Enregistrez ensuite vos informations d'identification dans le fichier app.config du projet. Modifiez le fichier app.config de façon à ce qu'il apparaisse de la même façon que dans l'exemple ci-après, en remplaçant `myaccount` par le nom de votre compte de stockage et `mykey` par la clé de votre compte de stockage :

    <?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [AZURE.NOTE] La dernière version de l'émulateur de stockage Azure ne prend pas en charge le stockage de fichiers. Votre chaîne de connexion doit cibler un compte de stockage Azure dans le cloud avec un accès à la version préliminaire des fichiers.


###Ajout de déclarations d'espaces de noms
Ouvrez le fichier program.cs à partir de l'Explorateur de solutions et ajoutez les déclarations d'espaces de noms en haut du fichier :

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

###Extraction de votre chaîne de connexion par programme
Vous pouvez extraire vos informations d'identification enregistrées dans le fichier app.config en utilisant la classe  `Microsoft.WindowsAzure.CloudConfigurationManager` ou  `System.Configuration.ConfigurationManager` . L''exemple présenté ici montre comment extraire vos informations d'identification à l'aide de la classe `CloudConfigurationManager` et les encapsuler avec la classe `CloudStorageAccount`. Ajoutez le code suivant à la méthode `Main()` dans program.cs :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

###Accès au partage de stockage de fichiers par programme

Ajoutez ensuite le code suivant à la méthode `Main()` après le code affiché ci-dessus afin d'extraire la chaîne de connexion. Ce code obtient une référence vers le fichier créé plus tôt et renvoie son contenu dans la fenêtre de console.

	//Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

	//Ensure that the share exists.
    if (share.Exists())
    {
		//Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

		//Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

		//Ensure that the directory exists.
        if (sampleDir.Exists())
        {
			//Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

			//Ensure that the file exists.
            if (file.Exists())
            {
				//Write the contents of the file to the console window.
                Console.WriteLine(file.DownloadTextAsync().Result);
            }
        }
    }

Exécutez l'application console pour voir le résultat.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage de fichiers, consultez les liens suivants
pour obtenir des informations plus détaillées.
<ul>
<li>Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de fichiers :
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Référence de la bibliothèque cliente de stockage pour .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dn167006.aspx">Référence de l'API REST du service de fichiers</a></li>
  </ul>
</li>
<li>Consultez les billets du blog de l'équipe Azure Storage relatifs au service de fichiers :
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Introduction au service de fichiers Microsoft Azure</a>
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">Conservation des connexions vers les fichiers Microsoft Azure</a></li>
  </ul>
</li><li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisez le <a href="/fr-FR/documentation/articles/storage-dotnet-how-to-use-blobs/">stockage d'objets blob</a> pour stocker des données non structurées.</li>
    <li>Utilisez le <a href="/fr-FR/documentation/articles/storage-dotnet-how-to-use-tables/">stockage de table</a> pour stocker des données structurées.</li>
    <li>Utilisez le <a href="/fr-FR/documentation/articles/storage-dotnet-how-to-use-queues/">stockage de files d'attente</a> pour stocker les messages de façon fiable.</li>
    <li>Utilisez une <a href="/fr-FR/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li>
  </ul>
</li>
</ul>

[Étapes suivantes]: #next-steps
[Présentation du stockage de fichiers]: #what-is-file-storage 
[Concepts du stockage de fichiers]: #file-storage-concepts
[Création d'un compte de stockage Azure]: #create-account
[Utilisation de PowerShell pour créer un partage de fichiers]: #use-cmdlets
[Montage du partage à partir d'une machine virtuelle Azure]: #mount-share
[Création d'une application locale pour accéder au stockage de fichiers]: #create-console-app

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png


\<!--HONumber=42-->
