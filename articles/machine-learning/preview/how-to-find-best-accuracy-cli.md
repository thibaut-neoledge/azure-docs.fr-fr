---
title: "Recherche d’exécutions présentant la précision maximale et la durée minimale dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Cas d’usage de bout en bout permettant de trouver la meilleure précision par le biais de l’interface de ligne de commande à l’aide d’Azure Machine Learning Workbench"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 9e5c2cc0b9ec17154c5280850d971308abfc2eb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-runs-with-the-best-accuracy-and-lowest-duration"></a>Trouver des exécutions précisément et rapidement
Dans le cas de plusieurs exécutions, l’un des cas d’usage consiste à rechercher les exécutions qui présentent la précision maximale. Une approche possible consiste à utiliser l’interface de ligne de commande (CLI) avec une requête [JMESPath](http://jmespath.org/). Pour plus d’informations sur l’utilisation de JMESPath dans Azure CLI, consultez [cet article](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). Dans l’exemple ci-après, quatre exécutions sont créés avec des valeurs de précision de 0, 0,98, 1 et 1. Les exécutions sont filtrées si elles figurent dans la plage `[MaxAccuracy-Threshold, MaxAccuracy]`, où `Threshold = .03`.

## <a name="sample-data"></a>Exemples de données
Si vous ne disposez d’aucune exécution dotée d’une valeur `Accuracy`, la procédure ci-après générera des exécutions pour l’interrogation :

Commencez par créer un fichier python dans Workbench, nommez-le `log_accuracy.py`, puis collez-y le code suivant :
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Ensuite, créez un fichier `run.py` et collez-y le code suivant :
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Enfin, ouvrez l’interface de ligne de commande par le biais de Workbench et exécutez la commande `python run.py` pour soumettre quatre expériences. Une fois le script terminé, quatre exécutions supplémentaires doivent apparaître dans l’onglet `Run History`.

## <a name="querying-the-run-history"></a>Interrogation de l’historique des exécutions
La première commande recherche la valeur de précision maximale.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

À l’aide de cette valeur de précision maximale de `1` et d’une valeur de seuil de `0.03`, la seconde commande filtre les exécutions au moyen de `Accuracy`, puis trie les exécutions par élément `duration` croissant.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Si vous voulez effectuer une vérification de limite supérieure stricte, le format de requête est le suivant : ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Si vous utilisez Powershell, le code ci-après utilisera les variables locales pour stocker le seuil et la précision maximale.
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la journalisation, consultez l’article [Guide pratique pour utiliser l’historique des exécutions et les métriques de modèles dans Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
