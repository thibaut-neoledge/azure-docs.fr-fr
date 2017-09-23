---
title: "Procédures stockées dans SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à l’implémentation de procédures stockées dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
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
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017

---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Procédures stockées dans SQL Data Warehouse
SQL Data Warehouse prend en charge plusieurs fonctionnalités Transact-SQL proposées par SQL Server. Plus important encore, il existe différentes fonctions, spécifiques à la montée en charge, que nous voulons exploiter pour optimiser les performances de notre solution.

Toutefois, pour assurer la mise à l’échelle et les performances de SQL Data Warehouse, il existe divers mécanismes et fonctions dont le comportement présente des différences, ainsi que d’autres qui ne sont pas pris en charge.

Cet article explique comment implémenter des procédures stockées dans SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Présentation des procédures stockées
Une procédure stockée est un excellent moyen d’encapsuler votre code SQL, en le stockant à un emplacement proche de vos données au sein de l’entrepôt de données. En encapsulant le code sous la forme d’unités pouvant être facilement gérées, les procédures stockées aident les développeurs à modulariser leurs solutions, afin d’optimiser la réutilisation du code. Chaque procédure stockée peut également accepter des paramètres, ce qui les rend encore plus flexibles.

SQL Data Warehouse fournit une implémentation simplifiée et rationalisée pour les procédures stockées. La plus grande différence par rapport à SQL Server est le fait que la procédure stockée ne correspond pas à du code précompilé. Dans les entrepôts de données, nous nous préoccupons généralement moins souvent de la durée de la compilation. Ce qui importe, c’est que le code de la procédure stockée soit optimisé comme il convient lorsqu’il est exécuté sur de grands volumes de données. Le gain de temps recherché se compte en heures, en minutes et en secondes, et non en millisecondes. De ce fait, il est plus utile de considérer les procédures stockées comme des conteneurs de logique SQL.     

Lorsque SQL Data Warehouse exécute votre procédure stockée, les instructions SQL sont analysées, traduites et optimisées au moment de l’exécution. Lors de ce processus, chaque instruction est convertie en différentes requêtes distribuées. Le code SQL réellement appliqué aux données est différent de la requête envoyée.

## <a name="nesting-stored-procedures"></a>Imbrication des procédures stockées
Lorsque les procédures stockées appellent d’autres procédures stockées ou exécutent un code SQL dynamique, la procédure stockée ou procédure d’appel de code centrale est considérée comme « imbriquée ».

SQL Data Warehouse prend en charge un maximum de 8 niveaux d’imbrication. En cela, il diffère légèrement de SQL Server, qui prend en charge 32 niveaux d’imbrication.

L’appel de procédure stockée de premier niveau correspond au niveau d’imbrication 1.

```sql
EXEC prc_nesting
```
Si la procédure stockée effectue également un autre appel EXEC, le niveau d’imbrication passe à 2.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Si la deuxième procédure exécute du code SQL dynamique, ce niveau monte à 3.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Notez que SQL Data Warehouse ne prend pas en charge @@NESTLEVEL. Vous devez conserver vous-même une trace de votre niveau d’imbrication. Il est peu probable que vous atteigniez le niveau d’imbrication 8. Cependant, si tel est le cas, vous devez modifier votre code et l’« aplatir », afin qu’il respecte cette limite.

## <a name="insertexecute"></a>INSERT... EXECUTE
SQL Data Warehouse ne vous permet pas d’utiliser le jeu de résultats d’une procédure stockée avec une instruction INSERT. Toutefois, vous pouvez utiliser une autre méthode.

Voir [Tables temporaires dans SQL Data Warehouse] pour consulter des exemples, afin de savoir comment procéder.

## <a name="limitations"></a>Limitations
Certains aspects des procédures stockées Transact-SQL ne sont pas implémentés dans SQL Data Warehouse.

Les voici :

* Procédures stockées temporaires
* Procédures stockées numérotées
* Procédures stockées étendues
* Procédures stockées CLR
* Option de chiffrement
* Option de réplication
* Paramètres table
* Paramètres en lecture seule
* Paramètres par défaut
* Contextes d’exécution
* Instruction RETURN

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la [vue d’ensemble du développement][development overview].

<!--Image references-->

<!--Article references-->
[Tables temporaires dans SQL Data Warehouse]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->

