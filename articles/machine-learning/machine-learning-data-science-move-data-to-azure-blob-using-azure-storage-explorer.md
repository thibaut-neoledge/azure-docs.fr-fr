---
title: Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure | Microsoft Docs
description: Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: bradsev

---
# Déplacer des données vers et depuis le stockage d’objets blob Azure à l’aide de l’explorateur de stockage Azure
Azure Storage Explorer est un outil Microsoft gratuit qui vous permet d’exploiter les données Azure Storage sous Windows, macOS et Linux. Cette rubrique décrit comment l’utiliser pour charger et télécharger des données depuis le stockage d’objets blob Azure. Vous pouvez télécharger l’outil sur le site [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

Des conseils sur les technologies utilisées pour déplacer des données vers et/ou depuis le stockage d’objets blob Azure sont disponibles ici :

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Si vous utilisez une machine virtuelle qui a été configurée avec les scripts fournis par les [machines virtuelles de science des données dans Azure](machine-learning-data-science-virtual-machines.md), cela signifie que l’Explorateur Azure Storage est déjà installé sur la machine virtuelle.
> 
> [!NOTE]
> Pour une présentation complète du stockage d’objets blob Azure, consultez les articles [Fonctionnalités de base des objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et [Service Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## Composants requis
Ce document suppose que vous disposez d’un abonnement Azure, d’un compte de stockage et de la clé de stockage correspondante pour ce compte. Avant de charger ou télécharger des données, vous devez connaître le nom et la clé de votre compte Azure Storage.

* Pour configurer un abonnement Azure, consultez [Essai gratuit pendant un mois](https://azure.microsoft.com/pricing/free-trial/).
* Pour obtenir des instructions sur la création d’un compte de stockage, ainsi que des informations sur le compte et la clé, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Notez la clé d’accès de votre compte de stockage, car vous avez besoin de cette clé pour vous connecter au compte avec l’outil Azure Storage Explorer.
* Vous pouvez télécharger l’outil Azure Storage Explorer sur le site [Microsoft Azure Storage Explorer](http://storageexplorer.com/). Acceptez les paramètres par défaut lors de l’installation.

<a id="explorer"></a>

## Utiliser Azure Storage Explorer
Les étapes suivantes expliquent comment charger ou télécharger des données à l’aide d’Azure Storage Explorer.

1. Lancez Microsoft Azure Storage Explorer.
2. Pour ouvrir l’Assistant **Connectez-vous à votre compte...**, sélectionnez l’icône **Paramètres de compte Azure**, puis **Ajouter un compte** et tapez vos informations d’identification. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Pour ouvrir l’Assistant **Connectez-vous à Azure Storage**, sélectionnez l’icône **Se connecter à Azure Storage**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Entrez la clé d’accès de votre compte de stockage Azure dans l’Assistant **Connectez-vous à Azure Storage**, puis cliquez sur **Suivant**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Entrez le nom du compte de stockage dans la zone **Nom du compte**, puis sélectionnez **Suivant**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Le compte de stockage ajouté doit maintenant apparaître dans la liste. Pour créer un conteneur d’objets blob dans un compte de stockage, cliquez avec le bouton droit de la souris sur le nœud **Conteneurs d’objets blob** de ce compte, sélectionnez **Créer un conteneur d’objets blob**, puis entrez un nom.
7. Pour charger des données dans un conteneur, sélectionnez le conteneur cible, puis cliquez sur le bouton **Charger**.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Cliquez sur **...** à droite de la zone **Fichiers**, sélectionnez un ou plusieurs fichiers à charger à partir du système de fichiers, puis cliquez sur **Charger** pour lancer le chargement des fichiers.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Pour télécharger les données, sélectionnez l’objet blob dans le conteneur correspondant à télécharger et cliquez sur **Télécharger**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

<!---HONumber=AcomDC_0914_2016-->