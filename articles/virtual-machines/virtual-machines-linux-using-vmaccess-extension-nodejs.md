---
title: "Réinitialiser l’accès sur des machines virtuelles Azure Linux à l’aide de l’extension VMAccess | Microsoft Docs"
description: "Réinitialisez l&quot;accès sur des machines virtuelles Azure Linux à l&quot;aide de l’extension VMAccess."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5574f726d90ff804ae40852ee20850150a179a00
ms.lasthandoff: 03/21/2017


---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Azure Linux à l'aide de l’extension VMAccess avec Azure CLI 1.0
Cet article vous explique comment utiliser l’extension Azure VMAccess pour vérifier ou réparer un disque, réinitialiser l’accès des utilisateurs, gérer les comptes d’utilisateur ou réinitialiser la configuration SSHD sous Linux. L’article requiert :

* un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).
* [l’interface de ligne de commande Azure (CLI)](../cli-install-nodejs.md) connectée à `azure login` ;
* l’interface de ligne de commande (CLI) Azure *doit être en* mode Azure Resource Manager `azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides
Il existe deux façons d’utiliser VMAccess sur vos machines virtuelles Linux :

* À l’aide de l’interface Azure CLI 1.0 et des paramètres requis.
* À l’aide de fichiers JSON bruts que VMAccess traite et exploite.

Pour la section sur la commande rapide, nous utiliserons la méthode Interface Azure CLI 1.0 `azure vm reset-access`. Dans les exemples de commandes suivants, remplacez les valeurs contenant « example » par les valeurs de votre propre environnement.

## <a name="create-a-resource-group-and-linux-vm"></a>Création d'un groupe de ressources et d’une machine virtuelle Linux
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Création d’une machine virtuelle Debian
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Réinitialisation du mot de passe racine
Pour réinitialiser le mot de passe racine :

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Réinitialisation de la clé SSH
Pour réinitialiser la clé SSH d'un utilisateur non racine :

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Créer un utilisateur
Pour créer un utilisateur :

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Supprimer un utilisateur
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Réinitialiser SSHD
Pour réinitialiser la configuration SSHD :

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Procédure pas à pas
### <a name="vmaccess-defined"></a>VMAccess défini :
Le disque de votre machine virtuelle Linux affiche des erreurs. Vous avez d'une certaine manière réinitialisé le mot de passe racine de votre machine virtuelle Linux ou supprimé accidentellement votre clé privée SSH. Dans les anciens centres de données, vous deviez aller sur place et ouvrir le KVM pour accéder à la console du serveur. Considérez l’extension Azure VMAccess comme ce commutateur KVM qui vous permet d’accéder à la console pour réinitialiser l’accès à Linux ou effectuer la maintenance au niveau du disque.

Pour la procédure pas à pas, nous allons utiliser la forme longue de VMAccess qui utilise des fichiers JSON bruts.  Ces fichiers JSON VMAccess peuvent également être appelés à partir de modèles Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Utilisation de VMAccess pour vérifier ou réparer le disque d'une machine virtuelle Linux
À l'aide de VMAccess, vous pouvez lancer une opération fsck sur le disque de votre machine virtuelle Linux.  Vous pouvez également effectuer une vérification de disque et une réparation de disque à l’aide d’un VMAccess.

Pour vérifier puis réparer le disque, utilisez ce script VMAccess :

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Exécutez le script VMAccess avec :

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Utilisation de VMAccess pour réinitialiser l'accès utilisateur à Linux
Si vous ne pouvez plus accéder à la racine de votre machine virtuelle Linux, vous pouvez lancer un script VMAccess pour réinitialiser le mot de passe racine.

Pour réinitialiser le mot de passe racine, utilisez ce script VMAccess :

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Exécutez le script VMAccess avec :

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Pour réinitialiser la clé SSH d'un utilisateur non racine, utilisez ce script VMAccess :

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Exécutez le script VMAccess avec :

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Utilisation de VMAccess pour gérer des comptes d'utilisateur sous Linux
VMAccess est un script Python qui peut servir à gérer les utilisateurs de votre machine virtuelle Linux sans connexion ni utilisation du compte sudo ou racine.

Pour créer un utilisateur, utilisez ce script VMAccess :

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Exécutez le script VMAccess avec :

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Pour supprimer un utilisateur, utilisez ce script VMAccess :

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Exécutez le script VMAccess avec :

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Utilisation de VMAccess pour réinitialiser la configuration SSHD
Si vous apportez des modifications à la configuration SSHD des machines virtuelles Linux et fermez la connexion SSH avant de vérifier vos changements, vous risquez de ne plus pouvoir retourner à votre configuration SSH.  VMAccess peut servir à rétablir la configuration SSHD avec des paramètres reconnus comme adéquats sans connexion par le biais de SSH.

Pour réinitialiser la configuration SSHD, utilisez ce script VMAccess :

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Exécutez le script VMAccess avec :

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Étapes suivantes
La mise à jour de Linux à l’aide d’extensions Azure VMAccess est une méthode permettant d’apporter des modifications à une machine virtuelle Linux en cours d’exécution.  Vous pouvez également utiliser des outils tels que cloud-init et des modèles Azure pour modifier votre machine virtuelle Linux au démarrage.

[À propos des extensions et des fonctionnalités des machines virtuelles](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Linux](virtual-machines-linux-extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


