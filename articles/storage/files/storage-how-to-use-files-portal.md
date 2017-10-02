---
title: "Gestion d’Azure Files à partir du portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser le portail Azure pour gérer Azure Files."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc521fa40428caacebb072cb1aacdebf0ce977ac
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Comment utiliser Azure Files à partir du portail Azure
Le [portail Azure](https://portal.azure.com) fournit une interface utilisateur pour la gestion d’Azure Files. Vous pouvez effectuer les actions suivantes à partir de votre navigateur web :

* Créer un partage de fichiers
* Charger et télécharger des fichiers vers et à partir de votre partage de fichiers.
* Surveiller l'utilisation réelle de chaque partage de fichiers.
* Ajuster le quota de la taille de partage.
* Copiez les commandes de montage servant à monter votre partage de fichiers à partir d’un client Windows ou Linux.

## <a name="create-file-share"></a>Créer un partage de fichiers
1. Connectez-vous au portail Azure.
2. Dans le menu de navigation, cliquez sur **Comptes de stockage** ou sur **Comptes de stockage (classiques)**.
    
    ![Capture d’écran montrant comment créer un partage de fichiers dans le portail](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Choisissez votre compte de stockage.

    ![Capture d’écran montrant comment créer un partage de fichiers dans le portail](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Choisissez le service Fichiers.

    ![Capture d’écran montrant comment créer un partage de fichiers dans le portail](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Cliquez sur Partages de fichiers et suivez le lien pour créer votre premier partage de fichiers.

    ![Capture d’écran montrant comment créer un partage de fichiers dans le portail](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Renseignez le nom du partage de fichiers ainsi que sa taille (jusqu’à 5 120 Go) pour créer votre premier partage de fichiers. Une fois le partage de fichiers créé, vous pouvez le monter à partir de n’importe quel système de fichiers prenant en charge SMB 2.1 ou SMB 3.0. Vous pouvez cliquer sur **Quota** sur le partage de fichiers pour modifier la taille du fichier jusqu’à 5 120 Go. Reportez-vous à la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût de stockage lié à l’utilisation d’Azure Files.

    ![Capture d’écran montrant comment créer un partage de fichiers dans le portail](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Charger et télécharger des fichiers
1. Choisissez un partage de fichiers que vous avez déjà créé.

    ![Capture d’écran montrant comment charger et télécharger des fichiers à partir du portail](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Cliquez sur **Charger** pour ouvrir l’interface utilisateur pour le chargement des fichiers.

    ![Capture d’écran montrant comment charger des fichiers à partir du portail](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Connexion au partage de fichiers
-  Cliquez sur **Se connecter** pour accéder à la ligne de commande permettant de monter le partage de fichiers à partir de Windows et Linux. Pour les utilisateurs de Linux, vous pouvez également consulter la section [Comment utiliser Azure Files avec Linux](../storage-how-to-use-files-linux.md) pour obtenir des instructions de montage supplémentaires pour les autres distributions Linux.

    ![Capture d’écran montrant comment monter le partage de fichiers](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  Vous pouvez copier les commandes servant à monter le partage de fichiers sous Windows ou Linux et exécuter ce dernier à partir de votre machine virtuelle Azure ou de votre machine locale.

    ![Capture d’écran qui montre les commandes de montage pour Windows et Linux](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Conseil :**  
Pour obtenir la clé d’accès du compte de stockage permettant le montage, cliquez sur **Afficher les clés d’accès pour ce compte de stockage** au bas de la page de connexion.

## <a name="see-also"></a>Voir aussi
Consultez ces liens pour en savoir plus sur Azure Files.

* [FAQ](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Résolution des problèmes sur Linux](storage-troubleshoot-linux-file-connection-problems.md)    
