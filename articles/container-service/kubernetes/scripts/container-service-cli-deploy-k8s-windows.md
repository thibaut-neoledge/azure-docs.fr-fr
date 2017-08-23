---
title: "Exemple de script Azure CLI - Créer un cluster ACS Windows Kubernetes | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer un cluster ACS Windows Kubernetes"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 9ca289817b54c39c59271f35a0af26bad2811da6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---

# <a name="create-an-azure-container-service-kubernetes-windows-cluster"></a>Créer un cluster Windows Kubernetes Azure Container Service

Cet exemple crée un cluster Azure Container Service exécutant Kubernetes pour des conteneurs basés sur Windows.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys \
  --admin-username azureuser \
  --admin-password Password12 \
  --windows
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#create) | Crée un cluster ACS. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Container Service dans la [documentation sur Azure Container Service](../cli-samples.md).

