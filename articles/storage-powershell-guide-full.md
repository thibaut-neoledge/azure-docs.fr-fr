<properties 
	pageTitle="Utilisation d'Azure PowerShell avec Azure Storage" 
	description="Découvrez comment utiliser Azure PowerShell pour Azure Storage" 
	services="storage" 
	documentationCenter="na" 
	authors="Selcin" 
	manager="adinah"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="selcint"/>

# Utilisation d'Azure PowerShell avec Azure Storage 

Guide Azure PowerShell pour Azure Storage

Vous pouvez utiliser plusieurs outils de développement et d'administration pour effectuer différentes tâches sur Azure Storage, par exemple le [portail de gestion Azure](http://manage.windowsazure.com/), la [bibliothèque cliente de stockage](http://msdn.microsoft.com/library/azure/dn261237.aspx), l'[API REST](http://msdn.microsoft.com/library/azure/dd179355.aspx) et [Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx). 

Dans ce guide, nous allons découvrir comment utiliser Azure PowerShell pour effectuer des tâches courantes sur le stockage Azure. Azure PowerShell est un module fournissant des applets de commande pour gérer Azure via Windows PowerShell. Il s'agit d'un interpréteur de ligne de commande et d'un langage de script basé sur des tâches, conçu spécialement pour l'administration système. Avec PowerShell, vous pouvez facilement contrôler et automatiser l'administration de vos services et de vos applications Azure. Par exemple, les applets de commande permettent d'effectuer les mêmes tâches que celles du portail de gestion Azure. 

Ce guide part du principe que vous avez une certaine expérience en tant qu'utilisateur d'[Azure Storage](http://azure.microsoft.com/documentation/services/storage/) et de [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Ce guide contient un certain nombre de scripts qui illustrent l'utilisation de PowerShell avec Azure Storage. Vous devez mettre à jour les variables du script en fonction de votre configuration avant d'exécuter chaque script.

La première section de ce guide fournit un aperçu rapide d'Azure Storage et de PowerShell. Pour obtenir des informations et des instructions détaillées, commencez par [Conditions préalables à l'utilisation d'Azure PowerShell avec Azure Storage](#pre).

## Sommaire
  
 - [Prise en main d'Azure Storage et de PowerShell en 5 minutes][]
 - [Conditions préalables à l'utilisation d'Azure PowerShell avec Azure Storage][]
 - [Gestion de comptes de stockage dans Azure][]
	 - [Création d'un abonnement Azure par défaut][]
	 - [Création d'un compte de stockage Azure][]
	 - [Définition d'un compte de stockage Azure par défaut][]
	 - [Affichage de la liste de tous les comptes de stockage Azure dans un abonnement][]
	 - [Création d'un contexte de stockage Azure][]
 - [Gestion des objets blob Azure et des instantanés d'objet blob][]
	 - [Création d'un conteneur][]
	 - [Chargement d'un objet blob dans un conteneur][]
	 - [Téléchargement d'objets blob depuis un conteneur][]
	 - [Copie d'objets blob d'un conteneur de stockage à un autre][]
	 - [Suppression d'un objet blob][]
	 - [Gestion des instantanés d'objet blob Azure][]
	 	- [Création d'un instantané d'objet blob][]
	 	- [Liste des instantanés d'un objet blob][]
	 	- [Copie d'un instantané d'objet blob][]
 - [Gestion des tables et des entités de table Azure][]
	 - [Création d'une table][]
	 - [Récupération d'une table][]
	 - [Suppression d'une table][]
	 - [Gestion des entités de table][]
	 	- [Ajout d'entités de table][]
	 	- [Interrogation d'entités de table][]
	 	- [Suppression d'entités de table][]
 - [Gestion des files d'attente et des messages de file d'attente Azure][]
	 - [Création d'une file d'attente][]
	 - [Récupération d'une file d'attente][]
	 - [Suppression d'une file d'attente][]
	 - [Gestion des messages de file d'attente][]
	 	- [Insertion d'un message dans une file d'attente][]
	 	- [Suppression du message suivant dans la file d'attente][]
 - [Gestion des partages de fichiers et des fichiers Azure][]
 - [Définition et interrogation de Storage Analytics][]
 - [Gestion de la signature d'accès partagé et de la stratégie d'accès stockée][]
 - [Utilisation d'Azure Storage pour le gouvernement des États-Unis et Azure en Chine][]
 - [Étapes suivantes][]
 
## <a name="getstart"></a>Prise en main d'Azure Storage et de PowerShell en 5 minutes
Cette section vous indique comment accéder à Azure Storage via PowerShell en 5 minutes. 

**Nouveau sur Azure :** obtenez un abonnement Microsoft Azure et un compte Microsoft associé à cet abonnement. Pour plus d'informations sur les options d'achat Azure, consultez [Version d'évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/), [Options d'achat](http://azure.microsoft.com/pricing/purchase-options/) et [Offres spéciales membres](http://azure.microsoft.com/pricing/member-offers/) (pour les membres MSDN, Microsoft Partner Network, BizSpark et autres programmes Microsoft).

**Si vous avez des doutes quant aux abonnements et aux comptes Microsoft Azure**, consultez la page [Gestion des comptes, des abonnements et des rôles d'administrateur](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**Après avoir créé un compte et un abonnement Microsoft Azure :**

1.	Téléchargez et installez [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) sur votre ordinateur local.
2.	Démarrez l'environnement d'écriture de scripts intégré de Windows PowerShell (ISE) : Dans votre ordinateur local, accédez au menu **Démarrer**. Tapez **outils d'administration** et cliquez pour exécuter ce composant. Dans la fenêtre **Outils d'administration**, cliquez avec le bouton droit sur **Windows PowerShell ISE**, cliquez sur **Exécuter en tant qu'administrateur**. 
3.	Dans **Windows PowerShell ISE**, cliquez sur **Fichier** > **Nouveau** pour créer un fichier script. 
4.	Maintenant, voici un script simple qui affiche les commandes PowerShell de base pour accéder à Azure Storage. Le script demande tout d'abord vos informations d'identification de compte Azure pour ajouter votre compte Azure à l'environnement PowerShell local. Ensuite, le script définit l'abonnement Azure par défaut et crée un nouveau compte de stockage dans Azure. Ensuite, le script crée un nouveau conteneur dans ce nouveau compte de stockage et charge un fichier image existant (blob) dans ce conteneur. Une fois que le script répertorie tous les objets blob de ce conteneur, il crée un répertoire de destination sur votre ordinateur local et télécharge le fichier image.
5.	Dans la section de code suivante, sélectionnez le script entre les remarques **#begin** et **#end**. Appuyez sur Ctrl+C pour le copier dans le Presse-papiers. 

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"
    
    	# Give a name to your new storage account. It must be lowercase! 
    	$StorageAccountName="yourstorageaccountname"
    
    	# Choose "West US" as an example.
    	$Location = "West US"
    
    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"
    
    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"
    
    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"
    
    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount
    
    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default
    
    	# Create a new storage account.
    	New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $Location
    
    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    
    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off
    
    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload 
    
    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName
    
    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName
    
    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  
    
    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder
    	#end
    
5.	Dans **Windows PowerShell ISE**, appuyez sur Ctrl+V pour copier le script. Cliquez sur **Fichier** > **Enregistrer**. Dans la boîte de dialogue **Enregistrer sous**, tapez le nom du fichier script, par exemple monscriptdestockage. Cliquez sur **Enregistrer**.

6.	Maintenant, vous devez mettre à jour les variables du script en fonction de vos paramètres de configuration. Vous devez mettre à jour la variable **$SubscriptionName** avec votre propre abonnement. Vous pouvez conserver les autres variables comme spécifié dans le script ou bien les modifier comme vous le souhaitez.

	- **$SubscriptionName :** vous devez mettre à jour cette variable avec votre propre nom d'abonnement. Appliquez l'une des trois méthodes suivantes pour rechercher le nom de votre abonnement :
	
		a. Dans **Windows PowerShell ISE**, cliquez sur **Fichier** > **Nouveau** pour créer un fichier de script. Copiez le script suivant dans le nouveau fichier de script et cliquez sur **Déboguer** > **Exécuter**. Le script demande tout d'abord vos informations d'identification de compte Azure pour ajouter votre compte Azure à l'environnement PowerShell local puis affiche tous les abonnements qui sont connectés à la session PowerShell locale. Notez le nom de l'abonnement que vous voulez utiliser dans ce didacticiel : 
	 
    		Add-AzureAccount 
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName 


		Actuellement, Azure prend en charge deux portails : le [portail de gestion Azure](https://manage.windowsazure.com/) actuel et le [portail Azure en version préliminaire](https://portal.azure.com/). Si vous vous connectez au [portail de gestion Azure](https://portal.azure.com/) actuel, faites défiler vers le bas et cliquez sur **Paramètres** sur le côté gauche du portail. Cliquez sur **Abonnements**. Copier le nom de l'abonnement que vous souhaitez utiliser lors de l'exécution des scripts de ce guide. Consultez l'exemple dans la capture d'écran suivante.

		![Azure Management Portal][Image1]

		Si vous vous connectez au [portail Azure en version préliminaire](https://portal.azure.com/), dans le menu Hub sur la gauche, cliquez sur **PARCOURIR**. Ensuite, cliquez sur **Tout**, puis sur **Abonnements**. Copier le nom de l'abonnement que vous souhaitez utiliser lors de l'exécution des scripts de ce guide. Consultez l'exemple dans la capture d'écran suivante.

		![Azure Preview Portal][Image2]
 

	- **$StorageAccountName :** utilisez le nom donné dans le script ou entrez un nouveau nom pour votre compte de stockage. **Important :** le nom du compte de stockage doit être unique dans Azure. Il doit aussi être en minuscules.
	 
	- **$Location :** utilisez le nom " Ouest des États-Unis " dans le script ou sélectionnez d'autres emplacements Azure, par exemple Est des États-Unis, Europe du Nord et ainsi de suite.
	  
	- **$ContainerName :** utilisez le nom donné dans le script ou entrez un nouveau nom pour votre conteneur.
	
	- **$ImageToUpload :** entrez le chemin d'accès à une image sur votre ordinateur local, par exemple C:\Images\image.png.
	
	- **$DestinationFolder :** entrez un chemin d'accès à un répertoire local pour stocker les fichiers téléchargés d'Azure Storage, par exemple " C:\Téléchargements ".

7.	Après la mise à jour des variables de script dans le fichier mystoragescript.ps1, cliquez sur **Fichier** > **Enregistrer**. Cliquez ensuite sur **Déboguer** > **Exécuter** ou appuyez sur **F5** pour exécuter le script.  

Après l'exécution du script, vous devez avoir un dossier local de destination qui inclut le fichier image téléchargé. La capture d'écran suivante présente un exemple de cette opération :

![Download Blobs][Image3]


> [AZURE.NOTE] La section Prise en main d'Azure Storage et de PowerShell en 5 minutes fournit une brève introduction sur l'utilisation d'Azure PowerShell avec Azure Storage. Pour plus d'informations et d'instructions, nous vous recommandons de lire les sections suivantes :

## <a name="pre"></a>Conditions préalables à l'utilisation d'Azure PowerShell avec Azure Storage
Vous avez besoin d'un abonnement Azure et d'un compte pour exécuter les applets de commande PowerShell présentées dans ce guide. Pour connaître les options d'abonnement Azure disponibles, consultez la page [Prise en main d'Azure](http://azure.microsoft.com/pricing/free-trial/).

Azure PowerShell est un module fournissant des applets de commande pour gérer Azure via Windows PowerShell. Pour plus d'informations sur l'installation et la configuration d'Azure PowerShell, consultez [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Nous vous recommandons de télécharger et d'installer le dernier module Azure PowerShell ou d'effectuer une mise à niveau vers celui-ci avant d'utiliser ce guide. 

Vous pouvez exécuter les applets de commande depuis la console Windows PowerShell Azure, depuis la console Windows PowerShell standard ou depuis la console Windows PowerShell ISE. Par exemple, pour ouvrir une **console Azure PowerShell**, allez dans le menu Démarrer, tapez Microsoft Azure PowerShell, puis cliquez avec le bouton droit et cliquez sur Exécuter en tant qu'administrateur. Pour ouvrir **Windows PowerShell ISE**, allez dans le menu Démarrer, tapez " outils d'administration ", puis cliquez pour l'exécuter. Dans la fenêtre Outils d'administration, cliquez avec le bouton droit sur Windows PowerShell ISE, cliquez sur Exécuter en tant qu'administrateur. Pour obtenir les options de configuration et d'installation détaillées, consultez la section Installation d'Azure PowerShell dans le didacticiel [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

## <a name="manageaccount"></a>Gestion de comptes de stockage dans Azure
### <a name="setdefsub"></a>Création d'un abonnement Azure par défaut
Pour gérer Azure Storage avec Azure PowerShell, vous devez authentifier votre environnement client avec Azure via l'authentification Azure Active Directory ou l'authentification basée sur les certificats. Pour plus d'informations, consultez la section [ Connexion à votre abonnement](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect) dans le didacticiel [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Ce guide utilise l'authentification Azure Active Directory :

1.	Dans la console Azure PowerShell ou dans Windows PowerShell ISE, tapez la commande suivante pour ajouter votre Azure compte à l'environnement PowerShell local :

    `Add-AzureAccount`

2.	Dans la fenêtre " Connectez-vous à Microsoft Azure ", tapez l'adresse électronique et le mot de passe associés à votre compte. Azure authentifie et enregistre les informations d'identification, puis ferme la fenêtre.

3.	Ensuite, exécutez la commande suivante pour voir les comptes Azure dans votre environnement PowerShell local et vérifiez que votre compte est répertorié :
 
	`Get-AzureAccount`
  
4.	Ensuite, exécutez l'applet de commande suivante pour voir tous les abonnements qui sont connectés à la session PowerShell locale et vérifiez que votre abonnement est répertorié :

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
 
5.	Pour définir un abonnement Azure par défaut, exécutez l'applet de commande Select-AzureSubscription :
 
	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default

6.	Vérifiez le nom de l'abonnement par défaut en exécutant l'applet de commande Get-AzureSubscription :

	`Get-AzureSubscription -Default`

7.	Pour installer toutes les applets de commande PowerShell pour Azure Storage, exécutez :

	`Get-Command -Module Azure -Noun *Storage*`

### <a name="createaccount"></a>Création d'un compte de stockage Azure
Pour utiliser Azure Storage, vous avez besoin d'un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour qu'il se connecte à votre abonnement. 

1.	Exécutez l'applet de commande Get-AzureLocation pour rechercher tous les emplacements de centre de données disponibles :

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	Ensuite, exécutez l'applet de commande New-AzureStorageAccount pour créer un nouveau compte de stockage. L'exemple suivant crée un compte de stockage dans le centre de données Ouest des États-Unis.
  
    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $location

Pour obtenir des informations détaillées, consultez [À propos des comptes Azure Storage](http://azure.microsoft.com/documentation/articles/storage-whatis-account/).

> [AZURE.IMPORTANT] Le nom du compte de stockage est unique dans Azure et est en minuscules. Pour connaître les conventions d'affectation de noms et les restrictions, consultez les pages [À propos des comptes Azure Storage](http://azure.microsoft.com/documentation/articles/storage-whatis-account/), [Affectation de noms et références aux conteneurs, objets blob et métadonnées](http://msdn.microsoft.com/library/azure/dd135715.aspx) et [Créer un compte de stockage](http://msdn.microsoft.com/library/azure/hh264518.aspx).

### <a name="defaultaccount"></a>Définition d'un compte de stockage Azure par défaut
Vous pouvez disposer de plusieurs comptes de stockage dans votre abonnement. Vous pouvez en choisir un et le définir comme compte de stockage par défaut pour toutes les commandes de stockage de la même session de PowerShell. Cela vous permet d'exécuter les commandes de stockage d'Azure PowerShell sans spécifier de manière explicite le contexte de stockage. 

1.	Pour définir un compte de stockage par défaut pour votre abonnement, vous pouvez exécuter l'applet de commande Select-AzureSubscription. 

		$SubscriptionName = "Your subscription name" 
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 

2.	Ensuite, exécutez l'applet de commande Get-AzureSubscription pour vérifier que le compte de stockage est associé à votre compte d'abonnement par défaut. Cette commande renvoie les propriétés d'abonnement de l'abonnement actuel, y compris son compte de stockage actif.

	    Get-AzureSubscription -Current

### <a name="listaccounts"></a>Affichage de la liste de tous les comptes de stockage Azure dans un abonnement
Chaque abonnement Azure peut comporter jusqu'à 100 comptes de stockage. Pour connaître les dernières informations sur les limites, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](http://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).

Exécutez l'applet de commande suivante pour rechercher le nom et l'état des comptes de stockage dans l'abonnement actuel :

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="createctx"></a>Création d'un contexte de stockage Azure
Le contexte de stockage Azure est un objet dans PowerShell qui permet d'encapsuler les informations d'identification de stockage. L'utilisation d'un contexte de stockage pendant l'exécution d'une applet de commande suivante vous permet d'authentifier votre demande sans spécifier explicitement le compte de stockage ou sa clé d'accès. Vous pouvez créer un contexte de stockage de plusieurs façons, par exemple à l'aide du nom du compte et de la clé d'accès, du jeton de signature d'accès partagé (SAS), de la chaîne de connexion ou de façon anonyme. Pour plus d'informations, consultez la page [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Utilisez l'une des trois méthodes suivantes pour créer un contexte de stockage :

- Exécutez l'applet de commande [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) pour connaître la clé d'accès de stockage principale pour votre compte de stockage Azure. Ensuite, appelez l'applet de commande [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) pour créer un contexte de stockage :

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generate a shared access signature token for an Azure storage container and use it to create a storage context:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	Pour plus d'informations sur SAP, consultez les pages [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) et [Signatures d'accès partagé, partie 1 : présentation du modèle SAS ](http://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

- Dans certains cas, vous pouvez spécifier les points de terminaison de service lorsque vous créez un nouveau contexte de stockage. Cela peut être nécessaire lorsque vous avez enregistré un nom de domaine personnalisé pour votre compte de stockage avec le service BLOB ou que vous souhaitez utiliser une signature d'accès partagé pour l'accès aux ressources de stockage. Définissez les points de terminaison de service dans une chaîne de connexion et utilisez-la pour créer un nouveau contexte de stockage comme indiqué ci-dessous :

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Pour plus d'informations sur la configuration d'une chaîne de connexion de stockage, consultez [Configuration des chaînes de connexion](http://msdn.microsoft.com/library/azure/ee758697.aspx).

Maintenant, vous avez configuré votre ordinateur et découvert comment gérer les abonnements et les comptes de stockage à l'aide d'Azure PowerShell. Allez à la section suivante pour découvrir comment gérer les objets blob Azure et les instantanés d'objet blob.

## <a name="manageblobs"></a>Gestion des objets blob Azure et des instantanés d'objet blob
Le stockage d'objets blob Azure est un service permettant de stocker de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n'importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts du service de stockage d'objets blob Azure. Pour plus d'informations, consultez les pages [Utilisation du stockage d'objets blob à partir de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs) et [Concepts de service BLOB](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="container"></a>Création d'un conteneur
Chaque objet blob du stockage Azure doit se trouver dans un conteneur. Vous pouvez créer un conteneur privé à l'aide de l'applet de commande New-AzureStorageContainer :

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Il existe trois niveaux d'accès en lecture anonyme : **Off**, **Blob** et **Container**. Pour empêcher tout accès anonyme aux objets blob, la valeur du paramètre d'autorisation est **Off**. Par défaut, le nouveau conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser les accès anonymes publics en lecture aux ressources de l'objet blob, mais pas aux métadonnées du conteneur ou à la liste d'objets blob du conteneur, la valeur du paramètre d'autorisation est **Blob**. Pour autoriser les accès anonymes publics complets aux ressources de l'objet blob, aux métadonnées du conteneur et à la liste d'objets blob du conteneur, la valeur du paramètre d'autorisation est **Container**. Pour plus d'informations, consultez l'énumération [BlobContainerPublicAccessType](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storageclient.blobcontainerpublicaccesstype(v=azure.95).aspx).

### <a name="uploadblob"></a>Chargement d'un objet blob dans un conteneur
Le service de stockage d'objets blob Azure prend en charge les objets blob de blocs et de page. Pour plus d'informations, consultez la page [Présentation des objets blob de blocs et de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pour charger des objets blob dans un conteneur, vous pouvez utiliser l'applet de commande [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx). Par défaut, cette commande charge les fichiers locaux vers un objet blob de blocs. Pour spécifier le type d'objet blob, vous pouvez utiliser le paramètre -BlobType. 

L'exemple suivant exécute l'applet de commande [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) pour obtenir tous les fichiers du dossier spécifié, puis les transmet à l'applet de commande suivante à l'aide de l'opérateur de pipeline. L'applet de commande [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) charge les fichiers locaux dans votre conteneur :

    Get-ChildItem -Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="downblob"></a>Téléchargement d'objets blob depuis un conteneur
L'exemple suivant montre comment télécharger des objets blob depuis un conteneur. Cet exemple établit d'abord une connexion à Azure Storage à l'aide du contexte de compte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès primaire. Ensuite, l'exemple récupère une référence d'objet blob à l'aide de l'applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx). Ensuite, l'exemple utilise l'applet de commande [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) pour télécharger les objets blob dans le dossier local de destination. 

    #Define the variables.
	ContainerName = "yourcontainername" 
    $DestinationFolder = "C:\DownloadImages" 
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Download blobs from a container. 
    New-Item -Path $DestinationFolder -ItemType Directory -Force 
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="copyblob"></a>Copie d'objets blob d'un conteneur de stockage à un autre
Vous pouvez également copier des objets blob entre les comptes de stockage et les régions de façon asynchrone. L'exemple suivant montre comment copier des objets blob d'un conteneur de stockage à un autre dans deux comptes de stockage différents. L'exemple définit d'abord des variables pour les comptes de stockage source et destination et crée ensuite un contexte de stockage pour chaque compte. Ensuite, l'exemple copie les objets blob du conteneur source vers le conteneur de destination à l'aide de l'applet de commande [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx). L'exemple suppose que les comptes de stockage source et destination existent déjà, ainsi que les conteneurs. 

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey
    
    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey
    
    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext
    
    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Notez que cet exemple effectue une copie asynchrone. Vous pouvez surveiller l'état de chaque copie en exécutant l'applet de commande [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx).

### <a name="deleteblob"></a>Suppression d'un objet blob
Pour supprimer un objet blob, commencez par obtenir une référence d'objet blob, puis exécutez l'applet de commande Remove-AzureStorageBlob. L'exemple suivant supprime tous les objets blob dans un conteneur. L'exemple définit tout d'abord les variables pour un compte de stockage, puis crée un contexte de stockage. Ensuite, l'exemple récupère la référence d'objet blob à l'aide de l'applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute l'applet de commande [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) pour supprimer les objets blob du conteneur dans le stockage Azure. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob 

## <a name="manageshots"></a>Gestion des instantanés d'objet blob Azure
Azure vous permet de créer l'instantané d'un objet blob. Un instantané est une version en lecture seule d'un objet blob capturé à un instant donné. Un instantané peut être lu, copié ou supprimé, mais pas modifié. Les instantanés sont une façon de sauvegarder un objet blob à un instant T. Pour plus d'informations, consultez [Création d'une capture instantanée d'un objet blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="createshot"></a>Création d'un instantané d'objet blob
Pour créer un instantané d'objet blob, commencez par obtenir une référence d'objet blob, puis appelez la méthode ICloudBlob.CreateSnapshot associée. L'exemple suivant définit tout d'abord les variables pour un compte de stockage, puis crée un contexte de stockage. Ensuite, l'exemple récupère la référence d'objet blob à l'aide de l'applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute la méthode [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) pour créer un instantané. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName
    
    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 

### <a name="listshot"></a>Liste des instantanés d'un objet blob
Vous pouvez créer autant d'instantanés que vous le souhaitez pour un objet blob. Vous pouvez lister les instantanés associés à votre objet blob pour effectuer le suivi de vos instantanés actuels. L'exemple qui suit utilise un objet blob prédéfini et appelle l'applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) pour obtenir la liste des instantanés de cet objet blob.  

    #Define the blob name.
    $BlobName = "yourblobname"
    
    #List the snapshots of a blob.
    Get-AzureStorageBlob -Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="copyshot"></a>Copie d'un instantané d'objet blob
Vous pouvez copier l'instantané d'un objet blob pour restaurer l'instantané. Pour plus d'informations et d'autres informations sur les restrictions, consultez [Création d'une capture instantanée d'un objet blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). L'exemple suivant définit tout d'abord les variables pour un compte de stockage, puis crée un contexte de stockage. Ensuite, il définit les variables de nom de conteneur et d'objet blob. L'exemple récupère la référence d'objet blob à l'aide de l'applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute la méthode [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) pour créer un instantané. Ensuite, l'exemple exécute l'applet de commande [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) pour copier l'instantané d'un objet blob à l'aide de l'objet ICloudBlob pour l'objet blob source. Vous devez mettre à jour les variables en fonction de votre configuration avant d'exécuter l'exemple. Notez que l'exemple suivant suppose que les conteneurs source et destination et l'objet blob source existent déjà. 

    #Définissez le compte de stockage et le contexte.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName
    
    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 
    
    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy -Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Vous avez appris à gérer les objets blob Azure et les instantanés d'objet blob avec Azure PowerShell. Allez à la section suivante pour découvrir comment gérer les tables, les files d'attente et les fichiers.

## <a name="managetables"></a>Gestion des tables et des entités de table Azure
Le service de stockage de tables Azure est un magasin de données NoSQL que vous pouvez utiliser pour stocker et interroger de grands ensembles de données non relationnelles structurées. Les principaux composants du service sont les tables, les entités et les propriétés. une table est une collection d'entités. Une entité est un ensemble de propriétés. Chaque entité peut comprendre jusqu'à 252 propriétés, qui sont toutes des paires nom-valeur. Cette section suppose que vous êtes déjà familiarisé avec les concepts du service de stockage de tables Azure. Pour plus d'informations, consultez les rubriques [Présentation du modèle de données du service de Table](http://msdn.microsoft.com/library/azure/dd179338.aspx) et [Utilisation du stockage de tables à partir de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

Dans les sous-sections suivantes, vous allez apprendrez à gérer le service de stockage de tables Azure à l'aide d'Azure PowerShell. Les scénarios abordés sont les suivants : **création**, **suppression** et **récupération** **de tables** et **ajout**, **interrogation** et **suppression** des entités d'une table.

### <a name="createtable"></a>Création d'une table
Toutes les tables doivent se trouver dans un compte de stockage Azure. L'exemple suivant montre comment créer une table dans Azure Storage. Cet exemple établit d'abord une connexion à Azure Storage à l'aide du contexte de compte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il utilise l'applet de commande [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) pour créer une table dans Azure Storage. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey 
    
    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="gettable"></a>Récupération d'une table
Vous pouvez interroger et récupérer une des tables ou toutes les tables dans un compte de stockage. L'exemple suivant montre comment récupérer une table à l'aide de l'applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx).

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable -Name $tabName -Context $Ctx 

Si vous appelez l'applet de commande Get-AzureStorageTable sans paramètres, il récupère toutes les tables de stockage pour un compte de stockage.

### <a name="remtable"></a>Suppression d'une table
Vous pouvez supprimer une table d'un compte de stockage à l'aide de l'applet de commande [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx).  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="mngentity"></a>Gestion des entités de table
Actuellement, Azure PowerShell ne fournit aucune applet de commande pour gérer directement les entités de table. Pour effectuer des opérations sur des entités de table, vous pouvez utiliser les classes fournies dans la [bibliothèque cliente Azure Storage pour .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx). 

#### <a name="addentity"></a>Ajout d'entités de table
Pour ajouter une entité à une table, commencez par créer un objet qui définit les propriétés de l'entité. Une entité peut comprendre jusqu'à 255 propriétés, y compris 3 propriétés système : **PartitionKey**, **RowKey** et **Timestamp**. Vous êtes chargé d'insérer et de mettre à jour les valeurs de **PartitionKey** et **RowKey**. Le serveur gère la valeur de **Timestamp**, qui ne peut pas être modifiée. Les propriétés **PartitionKey** et **RowKey** identifient de manière unique chaque entité d'une table. 

-	**PartitionKey** : Détermine la partition ou est stockée l'entité.
-	**RowKey** : identifie de façon unique l'entité dans la partition.

Vous pouvez définir au maximum 252 propriétés personnalisées par entité. Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).

L'exemple suivant montre comment ajouter des entités à une table. L'exemple montre comment récupérer la table des employés et comment y ajouter plusieurs entités. Il établit d'abord une connexion à Azure Storage à l'aide du contexte de compte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il récupère la table à l'aide de l'applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Si la table n'existe pas, il utilise l'applet de commande [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) pour créer une table dans Azure Storage. Ensuite, l'exemple définit une fonction Add-Entity personnalisée pour ajouter des entités à la table en spécifiant la partition et la clé de ligne de chaque entité. La fonction Add-Entity ajoute l'applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) à la classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) pour créer un objet d'entité. Plus tard, l'exemple appelle la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) sur cet objet d'entité pour l'ajouter à une table. 

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table, 
       [String]$partitionKey, 
       [String]$rowKey,
       [String]$name, 
       [Int]$id
    )
    
      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)
    
      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore
    
    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable -Name $TableName -Context $Ctx
    }
    
    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1 
    Add-Entity $table "Partition1" "Row2" "Jessie" 2 
    Add-Entity $table "Partition2" "Row1" "Christine" 3 
    Add-Entity $table "Partition2" "Row2" "Steven" 4 


#### <a name="queryentity"></a>Interrogation d'entités de table
Pour interroger une table, utilisez la classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx). L'exemple suivant suppose que vous avez déjà exécuté le script donné dans la section Ajout d'entités de table de ce guide. Cet exemple établit d'abord une connexion à Azure Storage à l'aide du contexte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il tente de récupérer la table Employees créée précédemment à l'aide de l'applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). L'appel de l'applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) sur la classe Microsoft.WindowsAzure.Storage.Table.TableQuery crée un nouvel objet de requête. L'exemple recherche les entités ayant une colonne ID dont la valeur est 1, comme spécifié dans un filtre de chaîne. Pour plus d'informations, consultez la section Construction des chaînes de filtre dans [Interrogation de tables et d'entités](http://msdn.microsoft.com/library/azure/dd894031.aspx). Lorsque vous exécutez cette requête, il retourne toutes les entités qui correspondent aux critères du filtre.    

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Get a reference to a table.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx
    
    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery
    
    #Define columns to select. 
    $list = New-Object System.Collections.Generic.List[string] 
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")
    
    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20
    
    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)
    
    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize 

#### <a name="deleteentity"></a>Suppression d'entités de table
Vous pouvez supprimer une entité en utilisant ses clés de partition et de ligne. L'exemple suivant suppose que vous avez déjà exécuté le script donné dans la section Ajout d'entités de table de ce guide. Cet exemple établit d'abord une connexion à Azure Storage à l'aide du contexte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il tente de récupérer la table Employees créée précédemment à l'aide de l'applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Si la table existe, l'exemple appelle la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) pour récupérer une entité en fonction de sa partition et de ses valeurs de clé de ligne. Il passe ensuite l'entité à la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) pour supprimer. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null) 
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="managequeues"></a>Gestion des files d'attente et des messages de file d'attente Azure
Les files d'attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n'importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts du service de stockage de files d'attente Azure. Pour plus d'informations, consultez les pages [Utilisation du stockage de files d'attente à partir de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/) et [Concepts de service de file d'attente](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/#concepts).

Cette section vous présente la gestion du service de stockage de files d'attente Azure à l'aide d'Azure PowerShell. Les scénarios traités incluent l'**insertion** et la **suppression** des messages de file d'attente, ainsi que la **création**, la **suppression** et la **récupération des files d'attente**.
 
### <a name="createqueue"></a>Création d'une file d'attente
L'exemple suivant établit d'abord une connexion à Azure Storage à l'aide du contexte de compte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il utilise l'applet de commande [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) pour créer une file d'attente appelée queuename. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue -Name $QueueName -Context $Ctx 

Pour plus d'informations sur les conventions d'affectation de noms pour le service de File d'attente Azure, consultez la page [Affectation de noms pour les files d'attente et les métadonnées](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="getqueue"></a>Récupération d'une file d'attente
Vous pouvez interroger et récupérer une file d'attente spécifique ou une liste de toutes les files d'attente dans un compte de stockage. L'exemple suivant montre comment récupérer une file d'attente spécifique à l'aide de l'applet de commande [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx).

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $Ctx 

Si vous appelez l'applet de commande [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sans paramètres, il récupère une liste de toutes les files d'attente.

### <a name="remqueue"></a>Suppression d'une file d'attente
Pour supprimer une file d'attente et tous les messages qu'elle contient, exécutez l'applet de commande Remove-AzureStorageQueue. L'exemple suivant montre comment supprimer une file d'attente spécifique à l'aide de l'applet de commande Remove-AzureStorageQueue. 

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue -Name $QueueName -Context $Ctx 

### <a name="mngqueuemsg"></a>Gestion des messages de file d'attente
Actuellement, Azure PowerShell ne fournit aucune applet de commande pour gérer directement les messages de file d'attente. Pour effectuer des opérations sur des messages de la file d'attente, vous pouvez utiliser les classes fournies dans la [bibliothèque cliente Azure Storage pour .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx). 

#### <a name="addqueuemsg"></a>Insertion d'un message dans une file d'attente
Pour insérer un message dans une file d'attente existante, commencez par créer une nouvelle instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Appelez ensuite la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Un CloudQueueMessage peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets.
 
L'exemple suivant montre comment ajouter un message à une file d'attente. Cet exemple établit d'abord une connexion à Azure Storage à l'aide du contexte de compte de stockage, ce qui inclut le nom de compte de stockage et sa clé d'accès. Ensuite, il récupère la file d'attente spécifiée à l'aide de l'applet de commande [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx). Si la file d'attente n'existe pas, l'applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) est utilisé pour créer une instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Plus tard, l'exemple appelle la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) sur cet objet de message pour l'ajouter à une file d'attente. Voici le code qui récupère une file d'attente et insère le message 'MessageInfo' :

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx 
    
    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"
    
       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    } 


#### <a name="dequeuemsg"></a>Suppression du message suivant dans la file d'attente
Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), vous obtenez le message suivant dans la file d'attente. Tout message renvoyé par un appel **GetMessage** n'est plus visible par les autres codes lisant les messages de cette même file d'attente. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **DeleteMessage** juste après le traitement du message.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx
    
    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)
    
    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage) 

## <a name="files"></a>Gestion des partages de fichiers et des fichiers Azure
Le stockage de fichiers Azure propose un stockage partagé pour les applications utilisant le protocole SMB 2.1. Les machines virtuelles et les services cloud Microsoft Azure peuvent partager des données de fichiers entre plusieurs composants d'application grâce à des partages montés. Les applications locales peuvent accéder aux données de fichiers d'un partage via l'API de stockage de fichiers ou via Azure PowerShell.

Pour plus d'informations sur le stockage Azure, consultez les pages [Utilisation du stockage de fichiers Azure](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/) et [Référence de l'API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx).

Pour savoir comment gérer le stockage de fichiers Azure à l'aide d'Azure PowerShell, consultez [Utilisation de PowerShell pour créer un partage de fichiers](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#use-cmdlets).

## <a name="stganalytics"></a>Définition et interrogation de Storage Analytics
[Azure Storage Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx) vous permet de recueillir des mesures (mesures de stockage) à partir de vos comptes de stockage Azure et d'enregistrer les données (journalisation de stockage) sur les demandes envoyées à votre compte de stockage. Vous pouvez utiliser des mesures de stockage pour analyser l'intégrité d'un compte de stockage et utiliser la journalisation de stockage pour diagnostiquer et résoudre les problèmes de votre compte de stockage.
Par défaut, les mesures de stockage ne sont pas activées pour vos services de stockage. Vous pouvez activer la surveillance à l'aide du portail de gestion Azure, de Windows PowerShell ou par programmation via une API de stockage. La journalisation du stockage se produit côté serveur. Elle vous permet d'enregistrer les détails des demandes ayant réussi et des demandes ayant échoué dans votre compte de stockage. Ces journaux permettent d'afficher les détails des opérations de lecture, d'écriture et de suppression sur vos tables, vos files d'attente et vos objets blob, ainsi que la raison de l'échec de certaines demandes.

Pour savoir comment activer et afficher les données de mesure de stockage à l'aide de PowerShell, consultez la page [Activation des mesures de stockage à l'aide de PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Pour savoir comment activer et récupérer des données de journalisation du stockage à l'aide de PowerShell, consultez 
[Activation de la journalisation de stockage avec PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) et [Rechercher les données de votre journalisation de stockage](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Pour plus d'informations sur l'utilisation de Storage Metrics et de la journalisation du stockage pour résoudre les problèmes de stockage, consultez la page [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).

## <a name="sas"></a>Gestion de la signature d'accès partagé et de la stratégie d'accès stockée
Les signatures d'accès partagé constituent une partie importante du modèle de sécurité pour toute application utilisant Azure Storage. Elles sont utiles pour fournir des autorisations d'accès limitées à votre compte de stockage aux clients qui ne doivent pas avoir la clé du compte. Par défaut, seul le propriétaire du compte de stockage peut accéder aux objets blob, aux tables et aux files d'attentes dans ce compte. Si votre service ou application doit mettre ces ressources à disposition d'autres clients sans partager votre clé d'accès, vous disposez de trois options :

- Définir les autorisations d'un conteneur afin de permettre un accès en lecture anonyme au conteneur et à ses objets blob. Cela n'est pas autorisé pour les tables ou les files d'attente.
- Utiliser une signature d'accès partagé qui accorde des droits d'accès restreints aux conteneurs, objets blob, files d'attente et tables pour un intervalle de temps spécifique.
- Utiliser une stratégie d'accès stockée pour obtenir un niveau de contrôle supplémentaire sur les signatures d'accès partagé pour un conteneur ou ses objets blob, une file d'attente ou une table. La stratégie d'accès stockée vous permet de modifier l'heure de début, l'heure d'expiration ou les autorisations d'une signature, ou de la révoquer après sa publication.

Une signature d'accès partagé peut prendre deux formes :

- **Signature d'accès partagé ad hoc** : lorsque vous créez une signature d'accès partagé ad hoc, l'heure de début, l'heure d'expiration et les autorisations associées à cette signature sont spécifiées sur l'URI de signature d'accès partagé. Ce type de signature d'accès partagé peut être créé sur un conteneur, un objet blob, une table ou une file d'attente, et il ne peut pas être révoqué.
- **Signature d'accès partagé avec stratégie d'accès stockée** : une stratégie d'accès stockée est définie sur un conteneur de ressources (un conteneur d'objets blob, une table ou une file d'attente) et vous pouvez l'utiliser pour gérer les contraintes pour une ou plusieurs signatures d'accès partagé. Lorsque vous associez une signature d'accès partagé à une stratégie d'accès stockée, la signature hérite des contraintes (heure de début, heure d'expiration et autorisations) définies pour la stratégie. Ce type de signature d'accès partagé peut être révoqué.

Pour plus d'informations, consultez [Signatures d'accès partagé](./storage-dotnet-shared-access-signature-part-1.md/), [Partie 1 : présentation du modèle SAP](./storage-dotnet-shared-access-signature-part-1.md/)et [Gestion de l'accès aux ressources d'Azure Storage](http://msdn.microsoft.com/library/azure/ee393343.aspx).

Dans les sections suivantes, vous allez découvrir comment créer un jeton de signature d'accès partagé et une stratégie d'accès stockée pour les tables Azure. PowerShell Azure fournit aussi des applets de commande semblables pour les conteneurs, les objets blob et les files d'attente. Pour exécuter les scripts de cette section, vous devez télécharger [Azure PowerShell version 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ou ultérieure.

### <a name="sub1"></a>Création d'un jeton de signature d'accès partagé basé sur une stratégie
Utilisez l'applet de commande New-AzureStorageTableStoredAccessPolicy pour créer une stratégie d'accès stockée. Ensuite, appelez l'applet de commande [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) pour créer un jeton de signature d'accès partagé basé sur une stratégie pour une table Azure Storage.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="sub2"></a>Création d'un jeton de signature d'accès partagé ad hoc (non révocable)
Utilisez l'applet de commande [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) pour créer un jeton de signature d'accès partagé ad hoc (non révocable) pour une table Azure Storage :

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="sub3"></a>Création d'une stratégie d'accès stockée
Utilisez l'applet de commande New-AzureStorageTableStoredAccessPolicy pour créer une stratégie d'accès stockée pour une table Azure Storage :

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="sub4"></a>Mise à jour d'une stratégie d'accès stockée
Utilisez l'applet de commande Set-AzureStorageTableStoredAccessPolicy pour mettre à jour une stratégie d'accès stockée existante pour une table Azure Storage :

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="sub5"></a>Suppression d'une stratégie d'accès stockée
Utilisez l'applet de commande Remove-AzureStorageTableStoredAccessPolicy pour supprimer une stratégie d'accès stockée sur une table Azure Storage :

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="gov"></a>Utilisation d'Azure Storage pour le gouvernement des États-Unis et Azure en Chine
Un environnement Azure est un déploiement indépendant de Microsoft Azure, par exemple [Azure Government pour le gouvernement des États-Unis](http://azure.microsoft.com/features/gov/), [AzureCloud pour Azure global](https://manage.windowsazure.com) et [AzureChinaCloud pour Azure exploité par 21Vianet en Chine](http://www.windowsazure.cn/). Vous pouvez déployer de nouveaux environnements Azure pour le gouvernement des États-Unis et Azure en Chine. 

Pour utiliser Azure Storage avec AzureChinaCloud, vous devez créer un contexte de stockage associé à AzureChinaCloud. Suivez ces étapes pour commencer :

1.	Exécutez l'applet de commande [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) pour voir les environnements Azure disponibles :

    `Get-AzureEnvironment`

2.	Ajoutez un compte Azure Chine à Windows PowerShell :

    `Add-AzureAccount -Environment AzureChinaCloud`

3.	Créez un contexte de stockage pour un compte AzureChinaCloud :

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Pour utiliser Azure Storage avec [Azure pour le gouvernement des États-Unis](http://azure.microsoft.com/features/gov/), vous devez définir un nouvel environnement, puis créer un contexte de stockage avec cet environnement :

1. Appelez l'applet de commande [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) pour créer un nouvel environnement Azure pour votre centre de données privé. 

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. Exécutez l'applet de commande [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) pour créer un nouveau contexte de stockage pour ce nouvel environnement, comme illustré ci-dessous. 
   
	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

Pour plus d'informations, consultez les pages suivantes :

- [Guide du développeur Microsoft Azure Government](../azure-government-developer-guide/). 
- [Différences entre AzureCloud pour Azure global et AzureChinaCloud pour Azure géré par 21Vianet en Chine](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next"></a>Étapes suivantes
Dans ce guide, vous avez appris comment gérer Azure Storage avec Azure PowerShell. Pour en savoir plus, consultez les articles et ressources suivants :

- [Documentation d'Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
- [Référence MSDN d'Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Applets de commande Azure Storage PowerShell](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Référence Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Prise en main d'Azure Storage et de PowerShell en 5 minutes]: #getstart
[Conditions préalables à l'utilisation d'Azure PowerShell avec Azure Storage]: #pre
[Gestion de comptes de stockage dans Azure]: #manageaccount
[Création d'un abonnement Azure par défaut]: #setdefsub
[Création d'un compte de stockage Azure]: #createaccount
[Définition d'un compte de stockage Azure par défaut]: #defaultaccount
[Affichage de la liste de tous les comptes de stockage Azure dans un abonnement]: #listaccounts
[Création d'un contexte de stockage Azure]: #createctx
[Gestion des objets blob Azure et des instantanés d'objet blob]: #manageblobs
[Création d'un conteneur]: #container
[Chargement d'un objet blob dans un conteneur]: #uploadblob
[Téléchargement d'objets blob depuis un conteneur]: #downblob
[Copie d'objets blob d'un conteneur de stockage à un autre]: #copyblob
[Suppression d'un objet blob]: #deleteblob
[Gestion des instantanés d'objet blob Azure]: #manageshots
[Création d'un instantané d'objet blob]: #createshot
[Liste des instantanés d'un objet blob]: #listshot
[Copie d'un instantané d'objet blob]: #copyshot
[Gestion des tables et des entités de table Azure]: #managetables
[Création d'une table]: #createtable
[Récupération d'une table]: #gettable
[Suppression d'une table]: #remtable
[Gestion des entités de table]: #mngentity
[Ajout d'entités de table]: #addentity
[Interrogation d'entités de table]: #queryentity
[Suppression d'entités de table]: #deleteentity
[Gestion des files d'attente et des messages de file d'attente Azure]: #managequeues
[Création d'une file d'attente]: #createqueue
[Récupération d'une file d'attente]: #getqueue
[Suppression d'une file d'attente]: #remqueue
[Gestion des messages de file d'attente]: #mngqueuemsg
[Insertion d'un message dans une file d'attente]: #addqueuemsg
[Suppression du message suivant dans la file d'attente]: #dequeuemsg
[Gestion des partages de fichiers et des fichiers Azure]: #files
[Définition et interrogation de Storage Analytics]: #stganalytics
[Gestion de la signature d'accès partagé et de la stratégie d'accès stockée]: #sas
[Utilisation d'Azure Storage pour le gouvernement des États-Unis et Azure en Chine]: #gov
[Étapes suivantes]: #next

<!--HONumber=47-->
