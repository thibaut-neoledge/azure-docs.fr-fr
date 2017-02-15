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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0 version préliminaire (az.py)
Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide de la commande [az vm create](/cli/azure/vm#create) au moyen d’Azure CLI 2.0 (version préliminaire). 

> [!NOTE] 
> La version préliminaire d’Azure CLI 2.0 est notre interface de ligne de commande multiplateforme nouvelle génération. [Faites un essai.](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> Le reste de notre documentation utilise la version Azure CLI existante. Pour créer une machine virtuelle à l’aide de la version Azure CLI 1.0 plutôt qu’avec CLI 2.0 Preview, consultez l’article [Créer une machine virtuelle à l’aide de l’interface de ligne de commande (CLI) Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour créer une machine virtuelle, vous devez disposer des éléments suivants : 

* un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/))
* La version [Azure CLI 2.0 (version préliminaire)](/cli/azure/install-az-cli2) installée
* Une connexion à votre compte Azure (par le biais de la commande [az login](/cli/azure/#login))

(Vous pouvez également déployer rapidement une machine virtuelle Linux à l’aide du [Portail Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

L’exemple ci-après indique comment déployer une machine virtuelle Debian et comment attacher votre clé SSH (Secure Shell) (vos arguments peuvent être différents ; si vous voulez une autre image, vous pouvez en [rechercher une](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Commencez par taper la commande [az group create](/cli/azure/group#create) pour créer votre groupe de ressources contenant la totalité de vos ressources déployées :

```azurecli
az group create -n myResourceGroup -l westus
```

La sortie se présente comme suit (vous pouvez choisir une autre option `--output` si vous le souhaitez) :

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Créer votre machine virtuelle à l’aide de la dernière image Debian

Vous pouvez désormais créer votre machine virtuelle et son environnement. N’oubliez pas de remplacer la valeur `----public-ip-address-dns-name` par une valeur unique ; il est possible que celle ci-dessous soit déjà utilisée.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


La sortie se présente comme suit. Notez la valeur `publicIpAddress` ou `fqdn` pour utiliser **ssh** dans votre machine virtuelle.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Connectez-vous à votre machine virtuelle à l’aide de l’adresse IP publique répertoriée dans la sortie. Vous pouvez également utiliser le nom de domaine complet (FQDN) répertorié.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

Vous devriez obtenir une sortie comparable à ce qui suit, selon la distribution que vous avez choisie :

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Étapes suivantes
La commande `az vm create` permet de déployer rapidement une machine virtuelle pour se connecter à un shell bash et être opérationnel. Toutefois, l’utilisation de `az vm create` ne vous confère pas un contrôle étendu et ne vous permet pas de créer un environnement plus complexe.  Pour déployer une machine virtuelle Linux personnalisée en fonction de votre infrastructure, lisez l’un des articles ci-dessous :

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Création d’une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vous pouvez également [utiliser le pilote Azure `docker-machine` avec plusieurs commandes pour créer rapidement une machine virtuelle Linux comme un hôte Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et, si vous utilisez Java, essayez la méthode [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Jan17_HO1-->


