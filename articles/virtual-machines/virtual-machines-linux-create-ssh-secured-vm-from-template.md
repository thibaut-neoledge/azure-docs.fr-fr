---
title: "Création d’une machine virtuelle Linux à l’aide d’un modèle Azure | Microsoft Docs"
description: "Créez une machine virtuelle Linux sur Azure à l’aide d’un modèle Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ff7ea65c329a37acf8b2febb52fd140954d81e97


---
# <a name="create-a-linux-vm-using-an-azure-template"></a>Créer une machine virtuelle Linux à l’aide d’un modèle Azure
Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide d’un modèle Azure.  L’article requiert :

* un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).
* [l’interface de ligne de commande Azure (CLI)](../xplat-cli-install.md) connectée à `azure login` ;
* l’interface de ligne de commande (CLI) Azure *doit être en *mode Azure Resource Manager`azure config mode arm`.

Vous pouvez également déployer rapidement un modèle de machine virtuelle Linux à l’aide du [Portail Azure](virtual-machines-linux-quick-create-portal.md).

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
L’exemple de code suivant montre comment appeler `azure group create` pour créer un groupe de ressources et déployer une machine virtuelle Linux sécurisée SSH simultanément en utilisant [ce modèle Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). N’oubliez pas que dans votre exemple, vous devez utiliser des noms propres à votre environnement. Cet exemple utilise `myResourceGroup` comme nom du groupe de ressources et `myVM` comme nom de machine virtuelle.

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




<!--HONumber=Nov16_HO2-->


