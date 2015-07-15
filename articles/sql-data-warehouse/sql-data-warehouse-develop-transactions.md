<properties
   pageTitle="Transactions dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’implémentation de transactions dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Transactions dans SQL Data Warehouse

Comme vous le savez, SQL Data Warehouse propose la prise en charge de l’ensemble des propriétés transactionnelles. Toutefois, pour garantir que les performances de SQL Data Warehouse sont maintenues à l’échelle, certaines fonctionnalités sont limitées, par rapport à SQL Server. Cet article identifie les différences et répertorie les autres éléments disponibles.

## Niveaux d’isolation des transactions
SQL Data Warehouse implémente les transactions ACID. Toutefois, l’isolation de la prise en charge des transactions se limite à `READ UNCOMMITTED`. Ce paramètre ne peut pas être modifié. Vous pouvez implémenter un certain nombre de méthodes de codage pour éviter les lectures erronées des données, si le problème se pose. Les méthodes les plus populaires reposent sur la commande CTAS et le basculement des partitions de table (souvent appelé « modèle de fenêtre glissante ») afin d’empêcher les utilisateurs d’interroger les données en cours de préparation. On utilise également des vues qui appliquent un filtre préliminaire aux données.

## État des transactions
SQL Data Warehouse utilise la fonction XACT_STATE() pour signaler l’échec d’une transaction, en utilisant la valeur -2. Cette valeur signifie que la transaction a échoué et est marquée pour une restauration uniquement.

> [AZURE.NOTE]L’association de la valeur -2 à la fonction XACT_STATE afin de signaler l’échec d’une transaction constitue un comportement différent par rapport à SQL Server. En effet, SQL Server utilise la valeur -1 pour indiquer qu’une transaction ne peut pas être validée. De plus, il peut tolérer la présence de certaines erreurs au sein d’une transaction sans pour autant signaler que cette dernière ne peut pas être validée. Par exemple, la valeur SELECT 1/0 entraîne une erreur, mais n’oblige pas la transaction à passer à l’état non validable. Par ailleurs, SQL Server autorise également les lectures dans une transaction non validable, ce que ne fait pas SQLDW. Si une erreur se produit dans une transaction SQLDW, cette dernière passe automatiquement à l’état -2, y compris les erreurs SELECT 1/0. Vous devez donc impérativement vérifier le code de votre application, afin de vous assurer qu’il utilise la fonction XACT_STATE().

Dans SQL Server, vous pouvez voir apparaître un fragment de code ressemblant à ce qui suit :

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

Notez que l’instruction `SELECT` survient avant l’instruction `ROLLBACK`. Par ailleurs, le paramètre de la variable `@xact` utilise l’instruction DECLARE, et non `SELECT`.

Dans SQL Data Warehouse, le code doit ressembler à ce qui suit :

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

Vous pouvez constater que la restauration de la transaction doit se produire avant la lecture des informations sur l’erreur, dans le bloc `CATCH`.

## Fonction Error_Line()
Il est également important de signaler que SQL Data Warehouse n’implémente pas et ne prend pas en charge la fonction ERROR_LINE(). Si cette fonction est incluse dans votre code, vous devez la supprimer pour respecter les exigences de SQL Data Warehouse. Placez plutôt des libellés de requête dans votre code pour implémenter les fonctionnalités équivalentes. Consultez l’article relatif aux [libellés de requête] pour en savoir plus.

## Utilisation des paramètres THROW et RAISERROR
Le paramètre THROW est l’implémentation la plus moderne du déclenchement d’exceptions dans SQL Data Warehouse. Toutefois, le paramètre RAISERROR est également pris en charge. Il existe cependant quelques différences, qu’il est préférable de prendre en compte.

- Les numéros associés aux messages d’erreur définis par l’utilisateur ne peuvent pas se trouver dans la place de valeurs allant de 100 000 à 150 000 dans le cas du paramètre THROW.
- Les messages d’erreurs associés au paramètre RAISERROR sont définis sur la valeur fixe de 50 000.
- L’utilisation de l’élément sys.messages n’est pas prise en charge.

## Limitations
En ce qui concerne les transactions, SQL Data Warehouse présente quelques restrictions supplémentaires.

Les voici :

- Les transactions distribuées ne sont pas acceptées.
- Les transactions imbriquées ne sont pas autorisées.
- Les points de sauvegarde ne sont pas acceptés.

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->