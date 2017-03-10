---
title: "Montage du Stockage Fichier Azure sur les machines virtuelles Linux à l’aide de SMB avec Azure CLI 1.0 | Microsoft Docs"
description: "Guide pratique de montage du Stockage Fichier Azure sur les machines virtuelles Linux à l’aide de SMB"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: ac0623a32fd1fb4a3dd9e1dd06f457da6ce56199
ms.lasthandoff: 02/27/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Montage du Stockage Fichier Azure sur les machines virtuelles Linux à l’aide de SMB avec Azure CLI 1.0

Cet article explique comment monter le Stockage Fichier Azure sur une machine virtuelle Linux en utilisant le protocole SMB (Server Message Block). Le Stockage Fichier propose des partages de fichiers dans le cloud qui s’appuient sur le protocole SMB standard. Les conditions requises sont :

* un [compte Azure](https://azure.microsoft.com/pricing/free-trial/) ;
* [des fichiers de clés SSH (Secure Shell) publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md).

## <a name="cli-versions-to-use"></a>Versions de l’interface CLI à utiliser
Vous pouvez mener à bien la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides
Pour réaliser rapidement cette tâche, suivez les étapes décrites dans cette section. Pour plus d’informations et de contexte, commencez à la section [« Procédure détaillée »](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

### <a name="prerequisites"></a>Composants requis
* un groupe de ressources ;
* un réseau virtuel Azure ;
* un groupe de sécurité réseau avec une connexion SSH entrante ;
* un sous-réseau ;
* un compte de stockage Azure ;
* des clés de compte de stockage Azure ;
* un partage de Stockage Fichier Azure ;
* une machine virtuelle Linux.

Remplacez les exemples par vos propres paramètres.

### <a name="create-a-directory-for-the-local-mount"></a>Créez un répertoire pour le montage local

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Monter le partage SMB de Stockage Fichier sur le point de montage

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Conserver le montage après redémarrage
Ajoutez la ligne suivante à `/etc/fstab` :

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

Le Stockage Fichier propose des partages de fichiers dans le cloud qui utilisent le protocole SMB standard. Avec la dernière version du Stockage Fichier, vous pouvez également monter un partage de fichiers à partir d’un système d’exploitation prenant en charge SMB 3.0. Un montage SMB sur Linux permet d’effectuer facilement une sauvegarde vers un emplacement de stockage d’archivage robuste et permanent pris en charge par un contrat de niveau de service.

Un bon moyen de déboguer les journaux consiste à déplacer les fichiers vers un montage SMB hébergé sur le Stockage Fichier à partir d’une machine virtuelle. En effet, un même partage SMB peut être monté localement sur votre station de travail Windows, Linux ou Mac. SMB ne constitue pas la meilleure solution pour transmettre en continu des journaux d’applications ou Linux en temps réel, car le protocole SMB n’est pas conçu pour gérer des tâches de journalisation aussi lourdes. Un outil de couche de journalisation unifié et dédié comme Fluentd resprésente un meilleur choix que SMB pour collecter la sortie de journalisation d’applications et Linux.

Dans cette procédure pas à pas détaillée, nous créons la configuration requise pour d’abord créer le partage de Stockage Fichier, puis le monter avec SMB sur une machine virtuelle Linux.

1. Créez un compte de stockage Azure avec le code suivant :

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Affichez les clés du compte de stockage.

    Quand vous créez un compte de stockage, les clés du compte sont créés par paires afin qu’elles puissent faire l’objet d’une rotation sans interruption du service. Lorsque vous basculez vers la deuxième clé de la paire, vous créez une nouvelle paire de clés. Les nouvelles clés de compte de stockage sont toujours créées par paires, pour garantir que vous disposiez toujours d’au moins une clé de stockage inutilisée prête au basculement. Pour afficher les clés du compte de stockage, utilisez le code suivant :

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Créez le partage de Stockage Fichier.

    Le partage de Stockage Fichier qui contient le partage SMB. Le quota est toujours exprimé en gigaoctets (Go). Pour créer le partage de Stockage Fichier, utilisez le code suivant :

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Créez le répertoire de point de montage.

    Vous devez créer un répertoire local sur le système de fichiers Linux pour monter le partage SMB. Tout ce qui est écrit ou lu à partir du répertoire de montage local est transféré au partage SMB hébergé sur le Stockage Fichier. Pour créer le répertoire, utilisez le code suivant :

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Montez le partage SMB avec le code suivant :

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Conservez le montage SMB d’un redémarrage à l’autre.

    Lorsque vous redémarrez la machine virtuelle Linux, le partage SMB monté est démonté au cours de l’arrêt. Pour remonter le partage SMB au démarrage, vous devez ajouter une ligne à /etc/fstab dans Linux. Linux utilise le fichier fstab pour lister les systèmes de fichiers à monter pendant le processus de démarrage. L’ajout du partage SMB garantit que le partage de Stockage Fichier est un système de fichiers monté définitivement pour la machine virtuelle Linux. Il est possible d’ajouter le partage SMB du Stockage Fichier sur une nouvelle machine virtuelle si vous utilisez cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande (CLI) Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

