<properties
   pageTitle="Procédures stockées dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’implémentation de procédures stockées dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Procédures stockées dans SQL Data Warehouse 

SQL Data Warehouse prend en charge plusieurs fonctionnalités Transact-SQL proposées par SQL Server. Plus important encore, il existe différentes fonctions, spécifiques à la montée en charge, que nous voulons exploiter pour optimiser les performances de notre solution.

Toutefois, pour assurer la mise à l’échelle et les performances de SQL Data Warehouse, il existe divers mécanismes et fonctions dont le comportement présente des différences, ainsi que d’autres qui ne sont pas pris en charge.

Cet article explique comment implémenter des procédures stockées dans SQL Data Warehouse.

## Présentation des procédures stockées
Une procédure stockée est un excellent moyen d’encapsuler votre code SQL, en le stockant à un emplacement proche de vos données au sein de l’entrepôt de données. En encapsulant le code sous la forme d’unités pouvant être facilement gérées, les procédures stockées aident les développeurs à modulariser leurs solutions, afin d’optimiser la réutilisation du code. Chaque procédure stockée peut également accepter des paramètres, ce qui les rend encore plus flexibles.

SQL Data Warehouse fournit une implémentation simplifiée et rationalisée pour les procédures stockées. La plus grande différence par rapport à SQL Server est le fait que la procédure stockée ne correspond pas à du code précompilé. Dans les entrepôts de données, nous nous préoccupons généralement moins souvent de la durée de la compilation. Ce qui importe, c’est que le code de la procédure stockée soit optimisé comme il convient lorsqu’il est exécuté sur de grands volumes de données. Le gain de temps recherché se compte en heures, en minutes et en secondes, et non en millisecondes. De ce fait, il est plus utile de considérer les procédures stockées comme des conteneurs de logique SQL.
 
Lorsque SQL Data Warehouse exécute votre procédure stockée, les instructions SQL sont analysées, traduites et optimisées au moment de l’exécution. Lors de ce processus, chaque instruction est convertie en différentes requêtes distribuées. Le code SQL réellement appliqué aux données est différent de la requête envoyée.

## Imbrication des procédures stockées
Lorsque les procédures stockées appellent d’autres procédures stockées ou exécutent un code SQL dynamique, la procédure stockée ou procédure d’appel de code centrale est considérée comme « imbriquée ».

SQL Data Warehouse prend en charge un maximum de 8 niveaux d’imbrication. En cela, il diffère légèrement de SQL Server, qui prend en charge 32 niveaux d’imbrication.

L’appel de procédure stockée de premier niveau correspond au niveau d’imbrication 1.

```
EXEC prc_nesting
``` 
Si la procédure stockée effectue également un autre appel EXEC, le niveau d’imbrication passe à 2 : ```
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```. Si la deuxième procédure exécute du code SQL dynamique, ce niveau monte à 3 : ```
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```.

Remarque : actuellement, SQL Data Warehouse ne prend pas en charge l’élément @@NESTLEVEL. Vous devez conserver une trace de votre niveau d’imbrication. Il est peu probable que vous atteigniez le niveau d’imbrication 8. Cependant, si tel est le cas, vous devez modifier votre code et l’« aplatir », afin qu’il respecte cette limite.

## INSERT... EXECUTE
SQL Data Warehouse ne vous permet pas d’utiliser le jeu de résultats d’une procédure stockée avec une instruction INSERT. Toutefois, vous pouvez utiliser une autre méthode.

Voir [Tables temporaires dans SQL Data Warehouse] pour consulter des exemples, afin de savoir comment procéder.

## Limitations

Certains aspects des procédures stockées Transact-SQL ne sont pas implémentés dans SQL Data Warehouse.

Les voici :

- Procédures stockées temporaires
- Procédures stockées numérotées
- Procédures stockées étendues
- Procédures stockées CLR
- Option de chiffrement
- Option de réplication
- Paramètres table
- Paramètres en lecture seule
- Paramètres par défaut
- Contextes d’exécution
- Instruction RETURN

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[Tables temporaires dans SQL Data Warehouse]: sql-data-warehouse-develop-temporary-tables.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/fr-FR/library/ms187371.aspx

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->