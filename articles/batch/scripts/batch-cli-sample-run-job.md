---
title: "Exemple de script Azure CLI : exécution d’un travail avec Batch | Microsoft Docs"
description: "Exemple de script Azure CLI : exécution d’un travail avec Batch"
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
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Exécution de travaux sur Azure Batch avec l’interface Azure CLI

Ce script crée une tâche Batch et ajoute une série de tâches au travail. Il explique également comment surveiller un travail et ses tâches. Enfin, il montre comment interroger le service Batch efficacement pour obtenir des informations sur les tâches du travail.

## <a name="prerequisites"></a>Conditions préalables

- Installez Azure CLI en suivant les instructions fournies dans le [Guide d’installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), si ce n’est déjà fait.
- Créez un compte Azure si vous n’en avez pas. Pour un exemple de script qui crée un compte, voir [Créer un compte Batch avec Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account).
- Configurez une application à s’exécuter à partir d’une tâche de démarrage si vous ne l’avez pas encore fait. Pour un exemple de script qui crée une application et charge un package d’application dans Azure, voir [Ajout d’applications dans Azure Batch avec Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application).
- Configurer un pool sur lequel le travail sera exécuté. Pour un exemple de script qui crée un pool avec une configuration de service cloud ou une configuration de machine virtuelle, voir [Gérer des pools Azure Batch avec CLI Azure](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/batch/run-job/run-job.sh "Exécuter un travail")]

## <a name="clean-up-job"></a>Travail de nettoyage

Après avoir exécuté l’exemple de script ci-dessus, lancez la commande suivante pour supprimer le travail et toutes les tâches associées. Notez que le pool doit être supprimé séparément. Pour plus d’informations sur la création et la suppression de pools, voir [Gérer des pools Azure Batch avec CLI Azure](./batch-cli-sample-manage-pool.md).

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un travail et des tâches Batch. Chaque commande de la table renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Permet de s’authentifier avec un compte Batch.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Crée un travail Batch.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Met à jour les propriétés d’un travail Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Récupère les détails relatifs au travail Batch spécifié.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Ajoute une tâche au travail Batch spécifié.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Récupère les détails relatifs à une tâche du travail Batch spécifié.  |
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | Répertorie les tâches associées au travail spécifié.  |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Batch dans la [documentation relative à la CLI Azure Batch](../batch-cli-samples.md).

