---
title: Montage du partage de fichiers Azure via SMB avec MacOS | Microsoft Docs
description: "Découvrez comment monter un partage de fichiers Azure via SMB avec Mac OS."
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
ms.openlocfilehash: 6e71a13f99160fdd310be1e9a59717c9fecbf35d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montage du partage de fichiers Azure via SMB avec MacOS
[Azure Files](storage-files-introduction.md) est un service de Microsoft qui vous permet de créer et d’utiliser des partages de fichiers réseau dans Azure en utilisant la norme du secteur. Les partages de fichiers Azure peuvent être montés dans MacOS Sierra (10.12) et El Capitan (10.11). Cet article expose deux méthodes de montage d’un partage de fichiers Azure sur MacOS avec l’interface utilisateur Finder et à l’aide de Terminal.

> [!Note]  
> Avant de monter un partage de fichiers Azure via SMB, il est recommandé de désactiver la signature de paquet SMB. Dans le cas contraire, cela risque d’entraîner des dysfonctionnements lors de l’accès au partage de fichiers Azure depuis MacOS. Votre connexion SMB est chiffrée. Cela n’affecte donc pas la sécurité de votre connexion. À partir du terminal, utilisez les commandes suivantes pour désactiver la signature de paquet SMB, conformément à la[rubrique d’aide Apple sur la désactivation de la signature des paquets SMB](https://support.apple.com/HT205926) :  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Conditions préalables pour le montage d’un partage de fichiers Azure sous MacOS
* **Nom de compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin du nom du compte de stockage.

* **Clé du compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin de la clé de stockage primaire (ou secondaire). Actuellement, les clés SAS ne sont pas prises en charge pour le montage.

* **Assurez-vous que le port 445 est ouvert** : SMB communique via le port TCP 445. Sur votre machine client (Mac), vérifiez que votre pare-feu ne bloque pas le port TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montage d’un partage de fichiers Azure via Finder
1. **Ouvrez Finder** : Finder est ouvert par défaut sous MacOS, mais vous pouvez vérifier qu’il s’agit bien de l’application par défaut en cliquant sur l’icône MacOS (illustrée par un visage), dans la barre :  
    ![L’icône MacOS (visage)](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Sélectionnez « Se connecter au serveur » dans le Menu « Aller »** : à l’aide du chemin d’accès UNC requis dans les [conditions préalables](#preq), remplacez la double barre oblique inverse (`\\`) par `smb://` et toutes les autres barres obliques inverses (`\`) par des barres obliques normales (`/`). Votre lien doit se présenter comme suit : ![la boîte de dialogue « Se connecter au serveur »](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Lorsque vous y êtes invité, utilisez le nom du partage et la clé du compte de stockage comme nom d’utilisateur et comme mot de passe** : lorsque vous cliquez sur « Connexion », dans la boîte de dialogue « Se connecter au serveur », vous êtes invité à entrer le nom d’utilisateur et le mot de passe (votre nom d’utilisateur MacOS est automatiquement prérempli). Vous avez la possibilité de placer le nom de partage / la clé du compte de stockage dans votre trousseau MacOS.

4. **Utilisez le partage de fichiers Azure à votre guise** : après avoir remplacé le nom de partage et la clé du compte de stockage par le nom d’utilisateur et le mot de passe, vous pouvez monter le partage. Vous pouvez l’utiliser comme un partage de fichiers / un dossier local normal. Vous pouvez notamment faire glisser et déposer des fichiers dans le partage :

    ![Une capture instantanée d’un partage de fichiers Azure monté](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montage d’un partage de fichiers Azure via Terminal
1. Remplacez `<storage-account-name>` par le nom de votre compte de stockage. Lorsque vous y êtes invité, entrez la clé du compte de stockage comme mot de passe. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Utilisez le partage de fichiers Azure à votre guise** : le partage de fichiers Azure est monté sur le point de montage spécifié par la commande précédente.  

    ![Une capture instantanée du partage de fichiers Azure monté](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files.

* [Rubrique d’aide Apple : connexion au partage de fichiers sur Mac](https://support.apple.com/HT204445)
* [FAQ](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Résolution des problèmes sur Linux](storage-troubleshoot-linux-file-connection-problems.md)    
