---
title: "Affecter des variables dans SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à l’affectation de variables Transact-SQL dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 045d5148cd3f12dac63c961ccf7c953d355ed725
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017

---
# <a name="assign-variables-in-sql-data-warehouse"></a>Affecter des variables dans SQL Data Warehouse
Dans SQL Data Warehouse, les variables sont définies au moyen de l’instruction `DECLARE` ou `SET`.

Pour définir une valeur de variable, il existe différentes méthodes, parfaitement valables :

## <a name="setting-variables-with-declare"></a>Définition de variables via l’instruction DECLARE
Dans SQL Data Warehouse, l’initialisation de variables avec l’instruction DECLARE constitue l’une des méthodes les plus flexibles pour définir une valeur de variable.

```sql
DECLARE @v  int = 0
;
```

De plus, vous pouvez utiliser cette instruction pour définir plusieurs variables à la fois. En effet, vous ne pouvez pas utiliser les éléments `SELECT` et `UPDATE` pour exécuter ceci :

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

De plus, il n’est pas possible d’initialiser et d’utiliser une variable au sein de la même instruction DECLARE. Illustrons notre propos : la commande de l’exemple ci-dessous n’est **pas** autorisée, car l’élément @p1 est initialisé, mais également utilisé dans la même instruction DECLARE. Cela entraîne une erreur.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Définition de valeurs avec l’instruction SET
L’instruction SET est très couramment utilisée pour définir une variable unique.

Tous les exemples ci-dessous illustrent des modes de définition d’une variable avec l’instruction SET, qui sont parfaitement valables :

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Avec l’instruction SET, vous pouvez uniquement définir une variable à la fois. Toutefois, comme indiqué ci-dessus, les opérateurs composés sont autorisés.

## <a name="limitations"></a>Limitations
Vous ne pouvez pas utiliser les instructions SELECT et UPDATE pour attribuer des variables.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la [vue d’ensemble du développement][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

