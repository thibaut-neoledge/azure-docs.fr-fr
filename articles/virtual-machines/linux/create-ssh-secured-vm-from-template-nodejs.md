---
title: "Créer une machine virtuelle Linux en utilisant un modèle Azure avec Azure CLI 1.0 | Documents Microsoft"
description: "Créez une machine virtuelle Linux sur Azure à l’aide d’Azure CLI 1.0 et d’un modèle Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Comment créer une machine virtuelle Linux à l’aide d’Azure CLI 1.0 et d’un modèle Azure Resource Manager
Cet article explique comment déployer rapidement une machine virtuelle Linux en utilisant Azure 1.0 et un modèle Azure Resource Manager. L’article requiert :

* un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).
* [Azure CLI 1.0](../../cli-install-nodejs.md) connecté avec `azure login`.
* l’interface de ligne de commande (CLI) Azure *doit être en* mode Azure Resource Manager`azure config mode arm`.

Vous pouvez également déployer rapidement un modèle de machine virtuelle Linux à l’aide du [Portail Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-command-summary) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="quick-command-summary"></a>Résumé des commandes rapides
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas
Les modèles vous permettent de créer des machines virtuelles dans Azure avec des paramètres que vous pouvez personnaliser lors du lancement, tels que les noms d’utilisateur et les noms d’hôte. Pour cet article, nous lançons un modèle Azure utilisant une machine virtuelle Ubuntu ainsi qu’un groupe de sécurité réseau (NSG) avec le port 22 ouvert pour SSH.

Les modèles Azure Resource Manager sont des fichiers JSON qui peuvent être utilisés pour des tâches uniques simples telles que le lancement d’une machine virtuelle Ubuntu tel que réalisé dans cet article.  Les modèles Azure peuvent également être utilisés pour construire des configurations Azure complexes d’environnements entiers, comme des piles de test, de développement ou de déploiement de production.

## <a name="create-the-linux-vm"></a>Créer la machine virtuelle Linux
L’exemple de code suivant montre comment appeler `azure group create` pour créer un groupe de ressources et déployer une machine virtuelle Linux sécurisée SSH simultanément en utilisant [ce modèle Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). N’oubliez pas que dans votre exemple, vous devez utiliser des noms propres à votre environnement. Cet exemple utilise *myResourceGroup* en tant que nom de groupe de ressources, et *myVM* comme nom de machine virtuelle.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

La sortie doit ressembler au bloc de sortie suivant :

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Cet exemple a déployé une machine virtuelle en utilisant le paramètre `--template-uri` .  Vous pouvez également télécharger ou créer un modèle localement et le transmettre à l’aide du paramètre `--template-file` en indiquant le chemin d’accès au fichier de modèle en tant qu’argument. L’interface de ligne de commande Azure vous invite à entrer les paramètres requis par le modèle.

## <a name="next-steps"></a>Étapes suivantes
Consultez la [galerie de modèles](https://azure.microsoft.com/documentation/templates/) pour découvrir les infrastructures d’application à déployer ensuite.

