---
title: "Montage d’un partage de fichiers Azure et accès au partage dans Windows | Microsoft Docs"
description: "Montage d’un partage de fichiers Azure et accès au partage dans Windows."
services: storage
documentationcenter: na
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
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: a8e4900bee81763300d976f0c966d7d20662ca27
ms.contentlocale: fr-fr
ms.lasthandoff: 09/02/2017

---

# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Montage d’un partage de fichiers Azure et accès au partage dans Windows
Le [Stockage Fichier Azure](../storage-dotnet-how-to-use-files.md) est le système de fichiers dans le cloud, facile à utiliser, de Microsoft. Les partages de fichiers Azure peuvent être montés dans Windows et Windows Server. Cet article présente trois méthodes différentes de montage d’un partage de fichiers Azure sur Windows : avec l’interface utilisateur de l’Explorateur de fichiers, via PowerShell ou via l’invite de commandes. 

Pour monter un partage de fichiers Azure en dehors de la région Azure sur laquelle il est hébergé, par exemple localement ou dans une région Azure différente, le système d’exploitation doit prendre en charge SMB 3.0. 

Vous pouvez monter des partages de fichiers Azure sur une installation Windows en cours d’exécution dans une machine virtuelle Azure ou localement. Le tableau ci-dessous illustre les versions du système d’exploitation qui prennent en charge le montage des partages de fichiers dans un environnement donné :

| Version de Windows        | Version SMB | Version montable dans une machine virtuelle Azure | Montable en local |
|------------------------|-------------|-----------------------|----------------------|
| Windows 10<sup>1</sup>  | SMB 3.0 | Oui | Oui |
| Windows Server 2016    | SMB 3.0     | Oui                   | Oui                  |
| Windows 8.1            | SMB 3.0     | Oui                   | Oui                  |
| Windows Server 2012 R2 | SMB 3.0     | Oui                   | Oui                  |
| Windows Server 2012    | SMB 3.0     | Oui                   | Oui                  |
| Windows 7              | SMB 2.1     | Oui                   | Non                   |
| Windows Server 2008 R2 | SMB 2.1     | Oui                   | Non                   |

<sup>1</sup>Les versions 1507, 1511, 1607 et 1703 de Windows 10

> [!Note]  
> Nous vous conseillons de prendre la base de connaissances la plus récente pour votre version de Windows.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Conditions préalables pour le montage d’un partage de fichiers Azure avec Windows 
* **Nom de compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin du nom du compte de stockage.

* **Clé du compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin de la clé de stockage primaire (ou secondaire). Actuellement, les clés SAS ne sont pas prises en charge pour le montage.

* **Assurez-vous que le port 445 est ouvert** : le Stockage Fichier Azure utilise le protocole SMB. SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 à partir de la machine cliente.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Montage du partage de fichiers Azure avec l’Explorateur de fichiers
> [!Note]  
> Notez que les instructions ci-après sont affichées sur Windows 10 et peuvent différer légèrement sur les versions plus anciennes. 

1. **Ouvrez l’Explorateur de fichiers** : pour cela, accédez au menu Démarrer ou appuyez sur les touches Win+E.

2. **Accédez à l’élément « Ce PC » sur la gauche de la fenêtre. Cela modifie les menus disponibles dans le ruban. Dans le menu Ordinateur, sélectionnez « Connecter un lecteur réseau »**.
    
    ![Une capture d’écran du menu déroulant « Connecter un lecteur réseau »](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Copiez le chemin d’accès UNC depuis le volet « Connexion » dans le portail Azure** : découvrez en détail comment trouver ces informations, en cliquant [ici](storage-how-to-use-files-portal.md#connect-to-file-share).

    ![Le chemin d’accès UNC dans le volet de connexion Stockage Fichier Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **Sélectionnez la lettre de lecteur et entrez le chemin d’accès UNC.** 
    
    ![Une capture d’écran de la boîte de dialogue « Connecter un lecteur réseau »](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Utilisez le nom du compte de stockage avec le préfixe `Azure\` comme nom d’utilisateur et une clé de compte de stockage comme mot de passe.**
    
    ![Une capture d’écran de la boîte de dialogue des identifiants réseau](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Utilisez le partage de fichiers Azure à votre guise**.
    
    ![Le partage de fichiers Azure est désormais monté.](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Lorsque vous êtes prêt à démonter (ou déconnecter) le partage de fichiers Azure, il vous suffit de cliquer avec le bouton droit de la souris sur l’entrée du partage, sous « Emplacements réseau », dans l’Explorateur de fichiers et de sélectionner « Déconnecter »**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Montage du partage de fichiers Azure avec PowerShell
1. **Utilisez la commande suivante pour monter le partage de fichiers Azure** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` par les informations appropriées.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Utilisez le partage de fichiers Azure à votre guise**.

3. **Lorsque vous avez terminé, démontez le partage de fichiers Azure à l’aide de la commande suivante**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> Vous pouvez utiliser le paramètre `-Persist` sur `New-PSDrive` pour que le partage de fichiers Azure soit visible pour le reste du système d’exploitation lorsqu’il est monté.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Montage du partage de fichiers Azure avec l’invite de commandes
1. **Utilisez la commande suivante pour monter le partage de fichiers Azure** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` par les informations appropriées.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Utilisez le partage de fichiers Azure à votre guise**.

3. **Lorsque vous avez terminé, démontez le partage de fichiers Azure à l’aide de la commande suivante**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Vous pouvez configurer le partage de fichiers Azure de manière à ce qu’il se reconnecte automatiquement au redémarrage grâce aux identifiants persistants dans Windows. La commande suivante permet de conserver les informations d’identification :
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le stockage de fichiers Azure, consultez ces liens.

* [FAQ](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>Vidéos et articles conceptuels
* [Stockage Fichier Azure : un système de fichiers SMB dans le cloud sans friction pour Windows et Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Utilisation de Stockage Fichier Azure avec Linux](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-file-storage"></a>Outils pour le Stockage Fichier Azure
* [Utilisation de AzCopy avec Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Résolution des problèmes liés au stockage Fichier Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Résolution des problèmes liés au stockage Fichier Azure - Linux](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Billets de blog :
* [Le stockage de fichiers Azure est désormais mis à la disposition générale](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Dans le Stockage Fichier Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migration de données vers le fichier Azure](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Référence
* [Référence de la bibliothèque cliente de stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)

