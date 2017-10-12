---
title: "Exemples de destinations/sorties possibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples de destinations/sorties de données personnalisées avec la préparation des données Azure ML."
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
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-destination-connections-python"></a>Exemple de connexions de destination (Python) 
Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md)


### <a name="write-to-excel"></a>Écriture dans Excel 


L’écriture dans Excel requiert une bibliothèque supplémentaire. L’ajout de nouvelles bibliothèques est documenté dans la présentation de l’extensibilité. `openpyxl` est la bibliothèque qui doit être ajoutée.

Avant de pouvoir écrire, d’autres modifications peuvent être nécessaires. Certains des types de données utilisés dans la préparation de données ne sont pas pris en charge sous certains formats de destination. Par exemple, si des objets « Erreur » existent, ils ne sont pas sérialisés correctement dans Excel. Par conséquent, une transformation « Remplacer les valeurs d’erreur » supprimant les erreurs des colonnes est nécessaire avant de tenter d’écrire dans Excel.

En supposant que toutes les tâches ci-dessus ont été effectuées, la ligne suivante écrit la table de données sur une même feuille dans un document Excel. Ajoutez une transformation d’écriture de flux de données (script), puis entrez le code suivant dans la section d’une expression :


#### <a name="on-windows"></a>Sur Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>Sur macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
