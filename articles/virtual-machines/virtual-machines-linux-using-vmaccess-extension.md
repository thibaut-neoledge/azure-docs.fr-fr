---
title: "Réinitialiser l’accès avec l’extension VMAccess et Azure CLI 2.0 | Microsoft Docs"
description: "Guide pratique de gestion des utilisateurs et de réinitialisation de l’accès sur des machines virtuelles Linux à l’aide de l’extension VMAccess et Azure CLI 2.0"
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
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: debdb8a16c8cfd6a137bd2a7c3b82cfdbedb0d8c
ms.openlocfilehash: 4fac98d37dde195af69d8bd03fd796c6eeae3734
ms.lasthandoff: 02/27/2017


---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Linux à l’aide de l’extension VMAccess avec Azure CLI 2.0
Le disque de votre machine virtuelle Linux affiche des erreurs. Vous avez d'une certaine manière réinitialisé le mot de passe racine de votre machine virtuelle Linux ou supprimé accidentellement votre clé privée SSH. Dans les anciens centres de données, vous deviez aller sur place et ouvrir le KVM pour accéder à la console du serveur. Considérez l’extension Azure VMAccess comme ce commutateur KVM qui vous permet d’accéder à la console pour réinitialiser l’accès à Linux ou effectuer la maintenance au niveau du disque.

Cet article vous explique comment utiliser l’extension Azure VMAccess pour vérifier ou réparer un disque, réinitialiser l’accès des utilisateurs, gérer les comptes d’utilisateur ou réinitialiser la configuration SSHD sous Linux. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](virtual-machines-linux-using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Méthodes d’utilisation de l’extension VMAccess
Il existe deux façons d’utiliser l’extension VMAccess sur vos machines virtuelles Linux :

* À l’aide de l’interface Azure CLI 2.0 et des paramètres requis.
* [À l’aide de fichiers JSON bruts que l’extension VMAccess va traiter](#use-json-files-and-the-vmaccess-extension) et exploiter.

Les exemples suivants utilisent [az vm access](/cli/azure/vm/access), ainsi que les paramètres appropriés. Pour suivre ces étapes, vous devez disposer de la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/#login).

## <a name="reset-ssh-key"></a>Réinitialisation d’une clé SSH
L’exemple suivant réinitialise la clé SSH pour l’utilisateur `azureuser` sur la machine virtuelle `myVM` :

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Réinitialiser le mot de passe
L’exemple suivant réinitialise le mot de passe pour l’utilisateur `azureuser` sur la machine virtuelle `myVM` :

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="reset-sshd"></a>Réinitialiser SSHD
L’exemple suivant réinitialise la configuration SSHD sur une machine virtuelle nommée `myVM` :

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>Créer un utilisateur
L’exemple suivant crée un utilisateur nommé `myNewUser` à l’aide de la clé SSH pour authentification sur la machine virtuelle nommée `myVM` :

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="deletes-a-user"></a>Supprime un utilisateur
L’exemple suivant supprime un utilisateur nommé `myNewUser` sur la machine virtuelle `myVM` :

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Utilisation de fichiers JSON et de l’extension VMAccess
Les exemples suivants utilisent des fichiers JSON bruts. Utilisez [az vm extension set](/cli/azure/vm/extension#set), puis appelez vos fichiers JSON. Ces fichiers JSON peuvent également être appelés à partir de modèles Azure. 

### <a name="reset-user-access"></a>Réinitialisation de l’accès utilisateur
Si vous ne pouvez plus accéder à la racine de votre machine virtuelle Linux, vous pouvez lancer un script VMAccess pour réinitialiser le mot de passe d’un utilisateur.

Pour réinitialiser la clé SSH d’un utilisateur, créez un fichier nommé `reset_ssh_key.json` avec le contenu suivant :

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Pour réinitialiser le mot de passe d’un utilisateur, créez un fichier nommé `reset_user_password.json` avec le contenu suivant :

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="reset-ssh"></a>Réinitialisation de SSH
Si vous apportez des modifications à la configuration SSHD des machines virtuelles Linux et fermez la connexion SSH avant de vérifier vos changements, vous risquez de ne plus pouvoir retourner à votre configuration SSH.  VMAccess peut servir à rétablir la configuration SSHD avec des paramètres reconnus comme adéquats sans connexion par le biais de SSH..

Pour réinitialiser la configuration SSHD, créez un fichier nommé `reset_sshd.json` avec le contenu suivant :

```json
{
  "reset_ssh": true
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-users"></a>Gestion des utilisateurs
VMAccess est un script Python qui peut servir à gérer les utilisateurs de votre machine virtuelle Linux sans connexion ni utilisation du compte sudo ou racine.

Pour créer un utilisateur, créez un fichier nommé `create_new_user.json` avec le contenu suivant :

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Pour supprimer un utilisateur, créez un fichier nommé `delete_user.json` avec le contenu suivant :

```json
{
  "remove_user":"myDeleteUser"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Vérification ou réparation du disque
À l'aide de VMAccess, vous pouvez lancer une opération fsck sur le disque de votre machine virtuelle Linux. Vous pouvez également effectuer une vérification de disque et une réparation de disque à l’aide d’un VMAccess.

Pour vérifier puis réparer le disque, utilisez ce script VMAccess, créez un fichier nommé `disk_check_repair.json` et ajoutez le contenu suivant :

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Étapes suivantes
La mise à jour de Linux à l’aide d’une extension Azure VMAccess est une méthode permettant d’apporter des modifications à une machine virtuelle Linux en cours d’exécution. Vous pouvez également utiliser des outils tels que cloud-init et des modèles Azure Resource Manager pour modifier votre machine virtuelle Linux au démarrage.

[À propos des extensions et des fonctionnalités des machines virtuelles](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Linux](virtual-machines-linux-extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


