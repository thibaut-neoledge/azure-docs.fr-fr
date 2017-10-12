---
title: Exemple de script CLI Azure - Gestion de pools dans Batch | Microsoft Docs
description: Exemple de script CLI Azure - Gestion de pools dans Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Gérer des pools Azure Batch avec l’interface CLI Azure

Ces scripts montrent certains des outils disponibles dans l’interface CLI Azure pour créer et gérer des pools de nœuds de calcul dans le service Azure Batch.

> [!NOTE]
> Dans cet exemple, les commandes créent des machines virtuelles Azure. L’exécution de machines virtuelles engendre des frais sur votre compte. Pour réduire ces frais, supprimez les machines virtuelles lorsque vous avez terminé l’exécution de cet exemple. Consultez la page [Nettoyer les pools](#clean-up-pools).

Les pools Batch peuvent être configurés de deux manières, soit via une configuration de Services cloud (Windows uniquement), soit via une configuration de machine virtuelle (Windows et Linux). Les exemples de scripts ci-dessous montrent comment créer des pools avec les deux configurations.

## <a name="prerequisites"></a>Composants requis

- Installez Azure CLI en suivant les instructions fournies dans le [Guide d’installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), si ce n’est déjà fait.
- Créez un compte Azure si vous n’en avez pas. Pour un exemple de script qui crée un compte, voir [Créer un compte Batch avec Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account).
- Configurez une application à s’exécuter à partir d’une tâche de démarrage si vous ne l’avez pas encore fait. Pour un exemple de script qui crée une application et charge un package d’application dans Azure, voir [Ajout d’applications dans Azure Batch avec Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application).

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Pool avec exemple de script pour la configuration du service cloud

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Pool avec exemple de script pour la configuration de machine virtuelle

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

## <a name="clean-up-pools"></a>Nettoyer les pools

Après avoir exécuté l’exemple de script ci-dessus, exécutez la commande suivante pour supprimer les pools.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer et manipuler des pools Batch.
Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Permet de s’authentifier avec un compte Batch.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Permet de répertorier les applications disponibles dans le compte Batch.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Permet de créer un pool de machines virtuelles.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Permet de mettre à jour les propriétés d’un pool.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Permet de répertorier les SKU de l’agent de nœud et les informations de l’image.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Permet de redimensionner le nombre de machines virtuelles en cours d’exécution dans le pool spécifié.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Permet d’afficher les propriétés d’un pool.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | Permet de supprimer le pool spécifié.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Permet d’activer la mise à l’échelle automatique sur un pool et d’appliquer une formule.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Permet de désactiver la mise à l’échelle automatique sur un pool.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Permet de répertorier tous les nœuds de calcul dans le pool spécifié.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Permet de redémarrer le nœud de calcul spécifié.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Permet de supprimer des nœuds répertoriés dans le pool spécifié.  |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Batch dans la [documentation relative à la CLI Azure Batch](../batch-cli-samples.md).

