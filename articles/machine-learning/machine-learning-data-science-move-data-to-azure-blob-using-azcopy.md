<properties 
	pageTitle="Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide d’AzCopy | Microsoft Azure" 
	description="Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide d’AzCopy" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide d’AzCopy

Des conseils sur les technologies utilisées pour déplacer des données vers et/ou depuis le stockage d’objets blob Azure sont disponibles ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Introduction

AzCopy est un utilitaire de ligne de commande conçu pour charger, télécharger et copier des données avec efficacité vers et à partir d'un stockage de fichiers, d'objets blob et de tables Microsoft Azure.

Pour obtenir des instructions sur l’installation d’AzCopy et des informations supplémentaires sur son utilisation avec la plateforme Azure, consultez [Prise en maint de l’utilitaire de ligne de commande AzCopy](../storage-use-azcopy.md).

> [AZURE.NOTE]Si vous utilisez une machine virtuelle qui a été configurée avec les scripts fournis par les [machines virtuelles de science des données dans Azure](machine-learning-data-science-virtual-machines.md), cela signifie qu’AzCopy est déjà installé sur la machine virtuelle.

> [AZURE.NOTE]Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Composants requis

Ce document suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

- Pour configurer un abonnement Azure, consultez la section [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).
- Pour obtenir des instructions sur la création d’un compte de stockage, ainsi que des informations sur le compte et la clé, consultez [À propos des comptes de stockage Azure](../storage-create-storage-account.md).

## Téléchargement de fichiers vers un objet blob Azure

Pour télécharger un fichier, utilisez la ligne de commande suivante dans AzCopy.

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

## Téléchargement de fichiers à partir d'un objet blob Azure

Pour télécharger un fichier à partir d’un objet blob, utilisez la ligne de commande suivante dans AzCopy.

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

## Transfert d’objets blob entre des conteneurs Azure

Pour transférer des objets blob entre des conteneurs Azure, utilisez la commande suivante dans AzCopy.

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

## Conseils d'utilisation d’AzCopy

> [AZURE.TIP]1. Lors du chargement de fichiers, le paramètre /S charge les fichiers de manière récursive. Sans ce paramètre, les fichiers situés dans le sous-répertoire ne sont pas chargés. 2. Lors du téléchargement du fichier, /S recherche le conteneur de manière récursive jusqu’à ce que tous les fichiers du répertoire spécifié et de ses sous-répertoires ou que tous les fichiers répondant au critère spécifié dans le répertoire concerné et ses sous-répertoires soient téléchargés. 3. Vous ne pouvez pas spécifier un fichier de blob à télécharger, à l’aide du paramètre /Source. Pour télécharger un fichier spécifique, spécifiez le nom du fichier à télécharger à l’aide du paramètre /Pattern. Le paramètre /S peut être utilisé pour qu’AzCopy recherche un modèle de nom de fichier de manière récursive. Sans le paramètre /Pattern, AzCopy télécharge tous les fichiers de ce répertoire.

<!---HONumber=AcomDC_1203_2015-->