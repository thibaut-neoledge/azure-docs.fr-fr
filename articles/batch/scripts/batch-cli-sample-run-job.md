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
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Exécution de travaux sur Azure Batch avec l’interface Azure CLI

Ce script crée une tâche Batch et ajoute une série de tâches au travail. Il explique également comment surveiller un travail et ses tâches.
L’exécution de ce script suppose qu’un compte Batch a été défini, et qu’une application et un pool ont été configurés. Pour en savoir plus, consultez les [exemples de scripts](../batch-cli-samples.md) qui concernent chacune de ces rubriques.

Si nécessaire, installez l’interface Azure CLI en suivant les instructions du [Guide d’installation Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), puis exécutez `az login` pour vous connecter à Azure.

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/batch/run-job/run-job.sh "Exécuter un travail")]

## <a name="clean-up-job"></a>Travail de nettoyage

Après avoir exécuté l’exemple de script ci-dessus, lancez la commande suivante pour supprimer le travail et toutes les tâches associées. Notez que le pool doit être supprimé séparément. Pour en savoir plus, consultez le [didacticiel sur la gestion des pools](./batch-cli-sample-manage-pool.md).

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un travail et des tâches Batch. Chaque commande de la table renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Permet de s’authentifier avec un compte Batch.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | Crée un travail Batch.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Met à jour les propriétés d’un travail Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | Récupère les détails relatifs au travail Batch spécifié.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | Ajoute une tâche au travail Batch spécifié.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | Récupère les détails relatifs à une tâche du travail Batch spécifié.  |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Batch dans la [documentation relative à la CLI Azure Batch](../batch-cli-samples.md).

