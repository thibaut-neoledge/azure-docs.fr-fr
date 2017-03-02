---
title: "Montage du stockage de fichiers Azure sur les machines virtuelles Linux à l’aide de SMB avec Azure CLI 1.0 | Microsoft Docs"
description: "Procédure de montage du stockage de fichiers Azure sur les machines virtuelles Linux à l’aide de SMB."
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
ms.sourcegitcommit: 4d547f854df5d4801779e37ac5f22fb9551bd6bb
ms.openlocfilehash: 73151dba33e2c05a66176ef6b895339bce167128
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-10"></a>Montage du stockage de fichiers Azure sur les machines virtuelles Linux à l’aide de SMB avec Azure CLI 1.0

Cet article montre comment utiliser le service de stockage de fichiers Azure sur une machine virtuelle Linux à l’aide d’un montage SMB.  Le stockage de fichiers Azure propose des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard.  Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)

- [des fichiers de clés SSH publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0 (version préliminaire)](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides

Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes nécessaires. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Configuration requise : groupe de ressources, réseau virtuel, groupe de sécurité réseau avec SSH entrant, sous-réseau, compte de stockage Azure, clés de compte de stockage Azure, partage de stockage de fichiers Azure et une machine virtuelle Linux. Remplacez les exemples par vos propres paramètres.

Créez un répertoire pour le montage local

```bash
mkdir -p /mnt/mymountpoint
```

Montez le partage SMB de stockage de fichiers Azure sur le point de montage

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Pour conserver le montage après un redémarrage, ajoutez une ligne à `/etc/fstab`

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

Le stockage de fichiers Azure propose des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard.  Et avec la dernière version du stockage de fichiers, vous pouvez également monter un partage de fichiers à partir d’un système d’exploitation prenant en charge SMB 3.0.  L’utilisation d’un montage SMB sur Linux permet de sauvegarder facilement vers un emplacement de stockage d’archivage robuste et permanent pris en charge par un contrat de niveau de service.  

Le déplacement de fichiers à partir d’une machine virtuelle vers un montage SMB hébergé sur le stockage de fichiers Azure est un excellent moyen de déboguer les fichiers journaux car ce même partage SMB peut être monté localement sur votre poste de travail Windows, Linux ou Mac.  SMB ne constitue pas la meilleure solution pour diffuser des journaux d’applications ou Linux en temps réel car le protocole SMB n’est pas conçu pour des activités de journalisation aussi lourdes.  Un outil de couche de journalisation unifié dédié tel que Fluentd est un meilleur choix que SMB pour collecter la sortie de journalisation d’applications et de Linux.

Pour cette procédure pas à pas détaillée, nous créons la configuration requise pour d’abord créer le partage de stockage de fichiers Azure, puis le monter via SMB sur une machine virtuelle Linux.

## <a name="create-the-azure-storage-account"></a>Création du compte de stockage Azure

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>Affichage des clés de compte de stockage

Les clés de compte de stockage Azure sont créées par paires, lorsque le compte de stockage est créé.  Les clés de compte de stockage sont créés par paires afin que les clés puissent faire l’objet d’une rotation sans interruption du service.  Lorsque vous effectuez une rotation des clés pour obtenir la deuxième clé de la paire, vous créez une nouvelle paire de clés.  Les nouvelles clés de compte de stockage sont toujours créées par paires, pour garantir que vous disposiez toujours d’au moins une clé de stockage inutilisée prête à être utilisée.

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Création du partage de stockage de fichiers Azure

Créez le partage de stockage de fichiers, qui contient le partage SMB.  Le quota est toujours en gigaoctets (Go).

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Créez le répertoire de point de montage

Un répertoire local est requis sur le système de fichiers Linux pour monter le partage SMB.  Tout ce qui est écrit ou lu à partir du répertoire de montage local est transféré au partage SMB hébergé sur le stockage de fichiers Azure.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montage du partage SMB

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Conservation du montage SMB au cours des redémarrages

Lorsque vous redémarrez la machine virtuelle Linux, le partage SMB monté est démonté lors de l’arrêt.  Pour remonter le partage SMB au démarrage, vous devez ajouter une ligne à `/etc/fstab` dans Linux.  Linux utilise le fichier `fstab` pour répertorier les systèmes de fichiers qu’il doit monter pendant le démarrage.  L’ajout du partage SMB garantit que le partage de stockage de fichiers Azure est un système de fichiers monté définitivement pour la machine virtuelle Linux.  Il est possible d’ajouter le partage SMB du stockage de fichiers Azure sur une nouvelle machine à l’aide de `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure (CLI)](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

