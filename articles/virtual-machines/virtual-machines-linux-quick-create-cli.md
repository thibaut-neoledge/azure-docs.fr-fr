---
title: "Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire) | Microsoft Azure"
description: "Créez une machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire)."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 0fd7aa8f941adaeb9961fd0e4724161b9fe2eeee


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0 version préliminaire (az.py)
Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide de la commande [az vm create](/cli/azure/vm#create) au moyen de l’interface Azure CLI 2.0 (version préliminaire) et avec des disques gérés ainsi que des disques de comptes de stockage natifs.

> [!NOTE] 
> La version préliminaire d’Azure CLI 2.0 est notre interface de ligne de commande multiplateforme nouvelle génération. [Faites un essai.](https://docs.microsoft.com/cli/azure/install-az-cli2)
>
> Pour créer une machine virtuelle à l’aide de l’interface Azure CLI 1.0 existante plutôt qu’avec la version préliminaire d’Azure CLI 2.0, consultez l’article [Créer une machine virtuelle à l’aide de l’interface de ligne de commande (CLI) Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour créer une machine virtuelle, vous devez disposer des éléments suivants : 

* un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/))
* La version [Azure CLI 2.0 (version préliminaire)](/cli/azure/install-az-cli2) installée
* Une connexion à votre compte Azure (par le biais de la commande [az login](/cli/azure/#login))

(Vous pouvez également déployer une machine virtuelle Linux via le [portail Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

L’exemple suivant montre comment déployer une machine virtuelle Debian et s’y connecter à l’aide d’une clé SSH (Secure Shell). Vos arguments peuvent être différents ; si vous voulez une autre image, vous [pouvez en rechercher une](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Utilisation de disques gérés

Pour utiliser des disques gérés Azure, vous devez utiliser une région qui les prend en charge. Commencez par taper la commande [az group create](/cli/azure/group#create) pour créer votre groupe de ressources contenant la totalité de vos ressources déployées :

```azurecli
 az group create -n myResourceGroup -l westus
```

La sortie se présente comme suit (vous pouvez spécifier une autre option `--output` si vous souhaitez obtenir un format différent) :

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Créer votre machine virtuelle 
Vous pouvez désormais créer votre machine virtuelle et son environnement. N’oubliez pas de remplacer la valeur `--public-ip-address-dns-name` par une valeur unique ; il est possible que celle ci-dessous soit déjà utilisée.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


La sortie se présente comme suit. Notez la valeur `publicIpAddress` ou `fqdn` pour utiliser **ssh** dans votre machine virtuelle.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Connectez-vous à votre machine virtuelle à l’aide de l’adresse IP publique ou du nom de domaine complet (FQDN) indiqués dans la sortie.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

Vous devriez obtenir une sortie comparable à ce qui suit, selon la distribution que vous avez choisie :

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Pour savoir ce que vous pouvez faire d’autre avec votre nouvelle machine virtuelle utilisant des disques gérés, consultez la section [Étapes suivantes](#next-steps).

## <a name="using-unmanaged-disks"></a>Utilisation de disques non gérés 

Les machines virtuelles qui utilisent des disques de stockage non gérés présentent des comptes de stockage non gérés. Tout d’abord, tapez [az groupe create](/cli/azure/group#create) pour créer votre groupe de ressources qui contiendra toutes les ressources déployées :

```azurecli
az group create --name nativedisks --location westus
```

La sortie se présente comme suit (vous pouvez choisir une autre option `--output` si vous le souhaitez) :

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Créer votre machine virtuelle 

Vous pouvez désormais créer votre machine virtuelle et son environnement. N’oubliez pas de remplacer la valeur `--public-ip-address-dns-name` par une valeur unique ; il est possible que celle ci-dessous soit déjà utilisée.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-native-disk
```

La sortie se présente comme suit. Notez la valeur `publicIpAddress` ou `fqdn` pour utiliser **ssh** dans votre machine virtuelle.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Connectez-vous à votre machine virtuelle à l’aide de l’adresse IP publique ou du nom de domaine complet (FQDN), qui sont tous deux indiqués dans la sortie ci-dessus.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

Vous devriez obtenir une sortie comparable à ce qui suit, selon la distribution que vous avez choisie :

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Étapes suivantes
La commande `az vm create` permet de déployer rapidement une machine virtuelle pour se connecter à un shell bash et être opérationnel. Toutefois, l’utilisation de `az vm create` ne vous confère pas un contrôle étendu et ne vous permet pas de créer un environnement plus complexe.  Pour déployer une machine virtuelle Linux personnalisée en fonction de votre infrastructure, lisez l’un des articles ci-dessous :

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Création d’une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vous pouvez également [utiliser le pilote Azure `docker-machine` avec plusieurs commandes pour créer rapidement une machine virtuelle Linux comme un hôte Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et, si vous utilisez Java, essayez la méthode [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Feb17_HO2-->


