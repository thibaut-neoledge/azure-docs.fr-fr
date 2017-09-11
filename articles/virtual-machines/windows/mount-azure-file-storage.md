---
title: "Monter un stockage de fichiers Azure à partir d’une machine virtuelle Windows Azure | Microsoft Docs"
description: "Stockez des fichiers dans le cloud à l’aide du stockage de fichiers Azure, puis montez le partage de fichiers cloud à partir d’une machine virtuelle Azure."
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6ffb2d2da1e2439df6f5da543411e3c2c68d3435
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="use-azure-file-shares-with-windows-vms"></a>Utiliser des partages de fichiers Azure avec des machines virtuelles Windows 

Vous pouvez utiliser des partages de fichiers Azure pour stocker des fichiers et y accéder à partir de votre machine virtuelle. Par exemple, vous pouvez stocker un script ou un fichier de configuration d’application à partager entre toutes vos machines virtuelles. Dans cette rubrique, nous vous expliquons comment créer et monter un partage de fichiers Azure, puis charger et télécharger des fichiers.

## <a name="connect-to-a-file-share-from-a-vm"></a>Se connecter à un partage de fichiers à partir d’une machine virtuelle

Dans cette section, nous partons du principe que vous disposez déjà d’un partage de fichiers auquel vous souhaitez vous connecter. Si vous devez en créer un, consultez la section [Créer un partage de fichiers](#create-a-file-share) dans la suite de cette rubrique.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Comptes de stockage**.
3. Choisissez votre compte de stockage.
4. Dans la page **Vue d’ensemble**, sous **Services**, sélectionnez **Fichiers**.
5. Sélectionnez un partage de fichiers.
6. Cliquez sur **Connexion** pour ouvrir une page qui montre la syntaxe de ligne de commande permettant de monter le partage de fichiers à partir de Windows ou Linux.
7. Sélectionnez la syntaxe de la commande et collez-la dans le Bloc-notes ou un autre emplacement facilement accessible. 
8. Modifiez la syntaxe en supprimant le ** > ** de début et en remplaçant *[lettre de lecteur]* par la lettre du lecteur (par exemple, **Y:**) où vous souhaitez monter le partage de fichiers.
8. Connectez-vous à votre machine virtuelle et ouvrez une invite de commandes.
9. Collez la syntaxe de connexion modifiée et appuyez sur **Entrée**.
10. Une fois la connexion créée, vous obtenez le message **The command completed successfully** (La commande a été correctement exécutée).
11. Vérifiez la connexion en tapant la lettre du lecteur pour basculer vers celui-ci, puis tapez **dir** pour afficher le contenu du partage de fichiers.



## <a name="create-a-file-share"></a>Créer un partage de fichiers 
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Comptes de stockage**.
3. Choisissez votre compte de stockage.
4. Dans la page **Vue d’ensemble**, sous **Services**, sélectionnez **Fichiers**.
5. Dans la page Service de fichiers, cliquez sur **+ Partage de fichiers** pour créer votre premier partage de fichiers.
6. Renseignez le nom du partage de fichiers. Le nom des partages de fichiers peut inclure des lettres minuscules, des chiffres et des traits d’union. Le nom ne peut pas commencer par un trait d’union et vous ne pouvez pas utiliser plusieurs traits d’union à la suite. 
7. Spécifiez une limite pour la taille du fichier (5 120 Go maximum).
8. Cliquez sur **OK** pour déployer le partage de fichiers.
   
## <a name="upload-files"></a>Charger des fichiers
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Comptes de stockage**.
3. Choisissez votre compte de stockage.
4. Dans la page **Vue d’ensemble**, sous **Services**, sélectionnez **Fichiers**.
5. Sélectionnez un partage de fichiers.
6. Cliquez sur **Charger** pour ouvrir la page **Charger des fichiers**.
7. Cliquez sur l’icône de dossier pour sélection un fichier à charger dans votre système de fichiers local.   
8. Cliquez sur **Charger** pour charger le fichier dans le partage de fichiers.

## <a name="download-files"></a>Téléchargement de fichiers
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Comptes de stockage**.
3. Choisissez votre compte de stockage.
4. Dans la page **Vue d’ensemble**, sous **Services**, sélectionnez **Fichiers**.
5. Sélectionnez un partage de fichiers.
6. Cliquez avec le bouton droit sur le fichier et choisissez **Télécharger** pour le télécharger sur votre ordinateur local.
   

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer et gérer des partages de fichiers à l’aide de PowerShell. Pour plus d’informations, consultez [Prise en main du stockage de fichiers Azure sur Windows](../../storage/files/storage-dotnet-how-to-use-files.md).

