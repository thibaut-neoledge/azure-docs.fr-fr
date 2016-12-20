---
title: "Utiliser des libellés pour instrumenter des requêtes dans SQL Data Warehouse | Documents Microsoft"
description: "Conseils relatifs à l’utilisation de libellés pour instrumenter des requêtes dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c72cd2c80d9fcee3d9340c23a629451c54c9156


---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Utiliser des libellés pour instrumenter des requêtes dans SQL Data Warehouse
SQL Data Warehouse prend en charge le concept de « libellé de requête ». Avant de l’étudier plus avant, voici un exemple parlant :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Cette dernière ligne balise la chaîne « My Query Label » dans la requête. Cette action est particulièrement utile, car ce libellé peut être interrogé via des DMV. Elle nous fournit un mécanisme de suivi des requêtes posant des problèmes et nous permet d’identifier la progression de l’exécution d’une action ETL.

À ce niveau, une convention d’affectation de noms efficace s’avère très utile. Ainsi, un nom de type « PROJET : PROCÉDURE : INSTRUCTION : COMMENTAIRE » permet d’identifier une requête de manière unique dans l’ensemble du code lors du contrôle de code source.

Pour effectuer une recherche basée sur un libellé, vous pouvez utiliser la requête suivante, qui tire parti de DMV :

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Vous devez impérativement encapsuler les crochets ou les guillemets doubles entourant le libellé du mot lors de l’interrogation. En effet, le libellé est un mot réservé. Il entraîne une erreur s’il n’est pas délimité.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la [vue d’ensemble sur le développement][vue d’ensemble sur le développement].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


