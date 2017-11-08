---
title: "Exemple de script Azure CLI - Obtenir un groupe de ressources managé et redimensionner des machines virtuelles | Microsoft Docs"
description: "Exemple de script Azure CLI - Obtenir un groupe de ressources managé et redimensionner des machines virtuelles"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: c78d2646471e40d60972cf91cb5bbd351f71a66c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obtenir des ressources dans un groupe de ressources managé et redimensionner des machines virtuelles avec Azure CLI

Ce script récupère les ressources à partir d’un groupe de ressources managé et redimensionne les machines virtuelles dans ce groupe de ressources.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour déployer l’application managée. Chaque commande de la table renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Répertorier les applications managées. Fournir des valeurs de requête pour affiner les résultats. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Répertoriez les ressources. Fournir un groupe de ressources et des valeurs de requête pour affiner le résultat. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Mettre à jour la taille d’une machine virtuelle. |


## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).
