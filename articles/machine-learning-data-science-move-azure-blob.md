<properties 
	pageTitle="Déplacer des données vers et depuis  un stockage Azure Blob" 
	description="Déplacer des données vers et depuis  un stockage Azure Blob" 
	services="machine-learning" 
	documentationCenter="" 
	authors="sunliangms,sachouks" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,sachouks" />

# Déplacer des données vers et depuis un stockage Azure Blob

L'article sur les [exemples de scénarios de science des données Cloud](http://azure.microsoft.com/documentation/articles/machine-learning-data-science-plan-sample-scenarios) vous aide à déterminer les ressources dont vous avez besoin pour différents processus de science des données. Si vous devez déplacer des données vers ou depuis le stockage Azure Blob basé sur votre scénario, optez pour l'une des méthodes suivantes :

- [Utilisation d'Azure Storage Explorer](#explorer)
- [Utilisation de l'utilitaire de ligne de commande AzCopy](#AzCopy)
- [Utilisation d'Azure SDK dans Python](#PythonSDK)



> [AZURE.NOTE] Pour une présentation complète du stockage Azure Blob, consultez les articles [Fonctionnalités de base d'Azure Blob](storage-dotnet-how-to-use-blobs.md) et  [Concepts de service Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx). 

Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage. Pour des instructions sur la façon d'obtenir ces informations, consultez la section " Afficher, copier et regénérer les clés d'accès au stockage " de l'article [Gérer des comptes de stockage](storage-create-storage-account.md). Ce document suppose que vous avez un compte Azure Storage ainsi que la ou les clés de stockage correspondantes.

<a id="explorer"></a>
## Utiliser Azure Storage Explorer 

Azure Storage Explorer est un outil Windows gratuit qui permet d'examiner et de modifier des données dans un compte de stockage Azure. Il est téléchargeable sur le site [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/). Les étapes suivantes expliquent comment charger ou télécharger des données à l'aide d'Azure Storage Explorer. 

1.  Lancez Azure Storage Explorer. 
2.  Si le compte de stockage auquel vous souhaitez accéder ne figure pas dans Azure Storage Explorer, cliquez sur le bouton " Ajouter un compte " pour ajouter le compte. S'il est déjà ajouté, sélectionnez-le dans la liste déroulante " Sélectionner un compte de stockage ".  
![Create workspace][1]
<br>
3. Entrez le nom et la clé du compte de stockage, puis cliquez sur Ajouter le compte de stockage. Vous pouvez ajouter plusieurs comptes de stockage. Chaque compte s'affiche sur un onglet. Les conteneurs de ce compte de stockage apparaissent dans le panneau gauche. Sélectionnez un conteur pour en afficher les blobs dans le panneau droit.  
![Create workspace][2]
<br>
![Create workspace][3]
<br>
4. Chargez les données en cliquant sur le bouton " Charger ". Sélectionnez un ou plusieurs fichiers à charger à partir du système de fichiers, puis cliquez sur " Ouvrir " pour lancer le chargement.
5. Téléchargez les données en sélectionnant le blob dans le conteneur correspondant et en cliquant sur le bouton " Télécharger ".

<a id="AzCopy"></a>
## Utilisation d'AzCopy

AzCopy est un utilitaire de ligne de commande, qui permet de charger et télécharger des données. 

**Attention** Si vous utilisez une machine autre que la machine virtuelle créée plus tôt dans le processus de science des données Cloud, installez AzCopy en procédant comme suit : [Téléchargez et installez AzCopy](storage-use-azcopy.md#install).

####Exemples de chargement/téléchargement de fichiers vers/depuis des blobs :

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]   
> 1. Lors du chargement de fichiers, le paramètre /S charge les fichiers de manière récursive. Sans ce paramètre, les fichiers situés dans le sous-répertoire ne sont pas chargés.  
> 2. Lors du téléchargement du fichier, /S recherche le conteneur de manière récursive jusqu'à ce que tous les fichiers du répertoire spécifié et de ses sous-répertoires ou que tous les fichiers répondant au critère spécifié dans le répertoire concerné et ses sous-répertoires soient téléchargés.  
> 3.  Vous ne pouvez pas spécifier un fichier de blob à télécharger, à l'aide du paramètre /Source. Pour ce faire, indiquez le nom du fichier de blob à télécharger et déclarez le paramètre /Pattern. Le paramètre /S permet à AzCopy de rechercher un type de nom de fichier de manière récursive. Sans le paramètre /Pattern, AzCopy télécharge tous les fichiers de ce répertoire. 

Pour une utilisation détaillée d'AzCopy, consultez l'article [Prise en main de l'utilitaire de ligne de commande AzCopy](storage-use-azcopy.md#install).


<a id="PythonSDK"></a>
## Utiliser Python

Avec l'API Python fournie dans le SDK Azure, vous pouvez :

- Créer un conteneur.
- Charger un blob dans un conteneur.
- Télécharger des blobs.
- Dresser la liste des blobs dans un conteneur.
- Supprimer un blob.

Cette section décrit comment répertorier, charger et télécharger des blobs. Pour plus d'informations sur l'utilisation de l'API Python, consultez l'article [Comment utiliser le stockage Azure Blob à partir de Python](storage-python-how-to-use-blob-storage.md). 

> [AZURE.NOTE] Si vous utilisez une machine autre que la machine virtuelle créée précédemment dans le processus de science des données Cloud, vous devez installer le [SDK Python Azure](python-how-to-install.md) avant d'utiliser l'exemple de code ci-dessous.

###Charger les données dans le blob
Ajoutez la ligne suivante vers le début du code Python dans lequel vous souhaitez programmer l'accès au service Azure Storage :

	from azure.storage import BlobService

L'objet **BlobService** vous permet d'exploiter les conteneurs et les blobs. Le code suivant crée un objet BlobService à l'aide du nom et de la clé du compte de stockage. Remplacez le nom et la clé du compte de stockage par le nom et la clé de votre compte.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Pour charger les données dans un blob, utilisez les méthodes suivantes :
 
1. put\_block\_blob\_from\_path (charge le contenu d'un fichier situé à l'emplacement spécifié)
2. put\_block_blob\_from\_file (charge le contenu d'un fichier/flux déjà ouvert)
3. put\_block\_blob\_from\_bytes (charge un tableau d'octets)
4. put\_block\_blob\_from\_text (charge la valeur de texte indiquée, dans l'encodage spécifié)
 
L'exemple de code suivant charge un fichier local dans un conteneur :
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

L'exemple de code suivant charge tous les fichiers (à l'exception des sous-répertoires) d'un répertoire local dans le blob :

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

###Télécharger des données à partir d'un blob

Pour télécharger des données à partir d'un blob, utilisez les méthodes suivantes :
1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text 

Ces méthodes effectuent le traitement nécessaire lorsque les données dépassent 64 Mo. 

L'exemple de code suivant télécharge le contenu d'un blob d'un conteneur dans un fichier local : 

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

L'exemple de code suivant télécharge tous les blobs d'un conteneur. Il utilise list\_blobs pour obtenir la liste des blobs disponibles dans le conteneur et les télécharge dans un répertoire local. 

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!--HONumber=49-->