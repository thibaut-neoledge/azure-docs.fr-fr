<properties 
	pageTitle="Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure | Microsoft Azure" 
	description="Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure" 
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
	ms.date="06/14/2016"
	ms.author="bradsev" />

# Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure

Azure Storage Explorer est un outil Windows gratuit qui permet d’examiner et de modifier des données dans un compte de stockage Azure. Cette rubrique décrit comment l'utiliser pour charger et télécharger des données depuis le stockage d'objets blob Azure. Vous pouvez télécharger l’outil sur le site [Azure Storage Explorer](http://storageexplorer.com/).

Des conseils sur les technologies utilisées pour déplacer des données vers et/ou depuis le stockage d’objets blob Azure sont disponibles ici :
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
&nbsp;
 
> [AZURE.NOTE] Si vous utilisez une machine virtuelle qui a été configurée avec les scripts fournis par les [machines virtuelles de science des données dans Azure](machine-learning-data-science-virtual-machines.md), cela signifie que l’Explorateur Azure Storage est déjà installé sur la machine virtuelle.
 
&nbsp;
 
> [AZURE.NOTE] Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Composants requis

Ce document suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

- Pour configurer un abonnement Azure, consultez [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).
- Pour obtenir des instructions sur la création d'un compte de stockage et pour obtenir des informations de compte et de clé, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).


<a id="explorer"></a>
## Utiliser Azure Storage Explorer 

Les étapes suivantes expliquent comment charger ou télécharger des données à l’aide d’Azure Storage Explorer.

1.  Lancez Azure Storage Explorer. 
2.  Si le compte de stockage auquel vous souhaitez accéder ne figure pas dans Azure Storage Explorer, cliquez sur le bouton « Ajouter un compte » pour ajouter le compte. S’il est déjà ajouté, sélectionnez-le dans la liste déroulante « Sélectionner un compte de stockage ». ![Créer un espace de travail][1] <br>
3. Entrez le nom et la clé du compte de stockage, puis cliquez sur Ajouter le compte de stockage. Vous pouvez ajouter plusieurs comptes de stockage. Chaque compte s’affiche sur un onglet. Les conteneurs de ce compte de stockage apparaissent dans le panneau gauche. Sélectionnez un conteneur pour en afficher les objets blob dans le panneau droit. ![Create workspace][2] <br> ![Create workspace][3] <br>
4. Chargez les données en cliquant sur le bouton « Charger ». Sélectionnez un ou plusieurs fichiers à charger à partir du système de fichiers, puis cliquez sur « Ouvrir » pour lancer le chargement.
5. Téléchargez les données en sélectionnant l’objet blob dans le conteneur correspondant et en cliquant sur le bouton « Télécharger ».

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=AcomDC_0622_2016-->