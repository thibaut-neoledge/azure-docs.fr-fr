---
title: "Exemple de script Azure CLI - Déployer la pile LAMP dans un groupe de machines virtuelles identiques avec équilibrage de charge | Microsoft Docs"
description: "Utilisez une extension de script personnalisée pour déployer la pile LAMP dans un groupe de machines virtuelles identiques à charge équilibrée sur Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a95c534d8f2ad5e8e2c7bdfc2b11405c70312727
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---

# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Déployer la pile LAMP dans un groupe de machines virtuelles identiques à charge équilibrée

Cet exemple crée un groupe de machines virtuelles identiques et applique une extension qui exécute un script personnalisé pour déployer la pile LAMP sur chaque machine virtuelle du groupe identique.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Créer un groupe de machines virtuelles identiques avec une pile LAMP")]

## <a name="connect"></a>Connecter

Utilisez ce code pour voir comment vous connecter à vos machines virtuelles et à votre groupe identique.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Accéder au groupe de machines virtuelles identiques")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, le groupe identique et les machines virtuelles, ainsi que toutes les ressources associées.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#create) | Crée un groupe de machines virtuelles identiques |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Ajouter un point de terminaison à charge équilibrée |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#set) | Créer l’extension qui exécute le script personnalisé sur le déploiement d’une machine virtuelle |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#update-instances) | Exécutez le script personnalisé sur les instances de machine virtuelle qui ont été déployées avant l’application de l’extension au groupe identique. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#scale) | Augmentez le groupe identique en ajoutant des instances de machine virtuelle. Le script personnalisé est exécuté sur celles-ci lors de leur déploiement. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#list) | Obtenez les adresses IP des machines virtuelles créées par l’exemple. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#show) | Obtenez les ports frontal et principal utilisés par l’équilibrage de charge. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

