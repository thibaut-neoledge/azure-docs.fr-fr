---
title: "Création d’un partage de fichiers Azure | Microsoft Docs"
description: "Comment créer un partage de fichiers Azure dans le Stockage Fichier Azure à l’aide du portail Azure, de PowerShell et de l’interface de ligne de commande Azure CLI."
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
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b81701e2544ace092f007e5d98b3141e1f7da724
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>Création d’un partage de fichiers dans le Stockage Fichier Azure
Vous pouvez créer des partages de fichiers Azure à l’aide du [portail Azure](https://portal.azure.com/), des applets de commande PowerShell pour Stockage Azure, des bibliothèques clientes Stockage Azure ou de l’API REST de Stockage Azure. Ce didacticiel contient des informations concernant les procédures suivantes :
* [Création d’un partage de fichiers Azure avec le portail Azure](#Create file share through the Portal)
* [Création d’un partage de fichiers Azure avec PowerShell](#Create file share using PowerShell)
* [Création d’un partage de fichiers Azure avec CLI](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Composants requis
Pour créer un partage de fichiers Azure, vous pouvez utiliser un compte de stockage existant ou [créer un nouveau compte de stockage Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour créer un partage de fichiers Azure avec PowerShell, vous avez besoin de la clé de compte et du nom de votre compte de stockage. Si vous envisagez d’utiliser Powershell ou CLI, vous aurez besoin de la clé du compte de stockage.

## <a name="create-file-share-through-the-portal"></a>Création d’un partage de fichiers via le portail
1. **Accédez au panneau Compte de stockage dans le portail Azure** :    
    ![Panneau Compte de stockage](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Cliquez sur Ajouter un partage de fichiers** :    
    ![Cliquez sur Ajouter un partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Indiquez le nom et le quota. Actuellement, la limite de quota maximum est 5 To** :    
    ![Indiquez un nom et le quota souhaité pour le nouveau partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal3.png).

4. **Affichez votre nouveau partage de fichiers** : ![Affichez votre nouveau partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Importez un fichier** : ![Importez un fichier](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Parcourez le partage de fichiers et gérez vos répertoires et vos fichiers** : ![Parcourez le partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Création d’un partage de fichiers via PowerShell
Pour vous préparer à utiliser PowerShell, téléchargez et installez les applets de commande PowerShell Azure. Consultez la rubrique [Installation et configuration d’Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) pour des instructions sur l’installation et le point d’installation.

> [!Note]  
> Il est recommandé de télécharger et d’installer le dernier module Azure PowerShell ou d’effectuer une mise à niveau vers celui-ci.

1. **Créez un contexte pour le compte de stockage et la clé**. Le contexte regroupe la clé de compte et le nom du compte de stockage. Pour obtenir des instructions sur la copie de votre clé de compte à partir du [portail Azure](https://portal.azure.com/), voir [Afficher et copier les clés d’accès de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Créez un nouveau partage de fichiers** :    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Le nom de votre partage de fichiers doit être en minuscules. Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Création d’un partage de fichiers via l’Interface de ligne de commande (CLI)
1. **Pour vous préparer à utiliser une Interface de ligne de commande (CLI), téléchargez et installez Azure CLI.**  
    Consultez [Installation d’Azure CLI 2.0](/cli/azure/install-az-cli2.md) et [Prise en main d’Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md).

2. **Créez une chaîne de connexion vers le compte de stockage dans lequel vous souhaitez créer le partage.**  
    Remplacez ```<storage-account>``` et ```<resource_group>``` par le nom de votre compte de stockage et le groupe de ressources de l’exemple suivant.

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Créez le partage de fichiers**.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Connexion et montage du partage de fichiers – Windows](storage-how-to-use-files-windows.md)
* [Connexion et montage du partage de fichiers – Linux](../storage-how-to-use-files-linux.md)
* [Connexion et montage du partage de fichiers – MacOS](storage-how-to-use-files-mac.md)

Pour plus d’informations sur le stockage de fichiers Azure, consultez ces liens.

* [FAQ](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Résolution des problèmes sur Linux](storage-troubleshoot-linux-file-connection-problems.md)   
