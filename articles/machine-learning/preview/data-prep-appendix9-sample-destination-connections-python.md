---
title: "Exemples de destinations/sorties possibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples de destinations/sorties de données personnalisées avec la préparation des données Azure Machine Learning."
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
ms.openlocfilehash: ca9e7d8c318100ba498ee15be15e213905f1e8d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-destination-connections-python"></a>Exemple de connexions de destination (Python) 
Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Écriture dans Excel 


L’écriture dans Excel nécessite une bibliothèque supplémentaire. L’ajout de nouvelles bibliothèques est documenté dans la vue d’ensemble de l’extensibilité. `openpyxl` est la bibliothèque que vous devez ajouter.

Avant d’écrire dans Excel, d’autres modifications peuvent être nécessaires. Certains des types de données utilisés dans la préparation de données ne sont pas pris en charge dans certains formats de destination. Par exemple, si des objets « Erreur » existent, ils ne sont pas sérialisés correctement dans Excel. Par conséquent, avant de tenter d’écrire dans Excel, vous avez besoin d’une transformation « Remplacer les valeurs d’erreur », qui supprime les erreurs des colonnes.

Si toutes les tâches ci-dessus ont été effectuées, la ligne suivante écrit la table de données sur une même feuille dans un document Excel. Ajoutez une transformation Écriture d’un flux de données (Script). Ensuite, entrez le code suivant dans la section d’une expression.


### <a name="on-windows"></a>Sur Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>Sur macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
