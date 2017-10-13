---
title: "Exemples d’expressions de filtre possibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples d’expressions de filtre possibles avec la préparation des données Azure ML."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: a80e43f84b518a7c9ce609fbebe34c531e1ab187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-filter-expressions-python"></a>Exemple d’expressions de filtre (Python) 
Avant de lire cette annexe, consultez [Vue d’ensemble de l’extensibilité Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrer avec un test d’équivalence
Filtrer uniquement les lignes dont la valeur (numérique) de Col2 est supérieure à 4 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrer avec plusieurs colonnes 
Filtrer uniquement les lignes où Col1 contient la valeur « Good » et Col2 contient la valeur (numérique) 1 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Tester le filtre par rapport à null
Filtrer uniquement les lignes où Col1 a la valeur null 
```python
    pd.isnull(row["Col1"])
```
