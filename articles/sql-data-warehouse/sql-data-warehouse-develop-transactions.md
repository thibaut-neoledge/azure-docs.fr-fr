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
   ms.date="07/11/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Transactions dans SQL Data Warehouse

Comme vous le savez, SQL Data Warehouse prend en charge les transactions dans le cadre de la charge de travail de l’entrepôt de données. Toutefois, pour garantir que les performances de SQL Data Warehouse sont maintenues à l’échelle, certaines fonctionnalités sont limitées, par rapport à SQL Server. Cet article identifie les différences et répertorie les autres éléments disponibles.

## Niveaux d’isolation des transactions
SQL Data Warehouse implémente les transactions ACID. Toutefois, l’isolation de la prise en charge des transactions se limite à `READ UNCOMMITTED`. Ce paramètre ne peut pas être modifié. Vous pouvez implémenter un certain nombre de méthodes de codage pour éviter les lectures erronées des données, si le problème se pose. Les méthodes les plus populaires reposent sur la commande CTAS et le basculement des partitions de table (souvent appelé « modèle de fenêtre glissante ») afin d’empêcher les utilisateurs d’interroger les données en cours de préparation. On utilise également des vues qui appliquent un filtre préliminaire aux données.

## Taille de la transaction
Une transaction de modification de données unique est limitée en taille. Aujourd’hui, la limite est appliquée « par distribution ». Par conséquent, l’allocation totale peut être calculée en multipliant la limite par le nombre de distributions. Pour évaluer approximativement le nombre maximal de lignes dans la transaction, divisez la limite de la distribution par la taille totale de chaque ligne. Pour les colonnes à longueur variable, pensez à prendre une longueur de colonne moyenne au lieu d’utiliser la taille maximale.

Dans le tableau ci-dessous, les hypothèses suivantes ont été formulées :

* Une distribution égale des données s’est produite
* La longueur de ligne moyenne est de 250 octets

| [DWU][] | Limite par distribution (Go) | Nombre de distributions | Taille de transaction MAX (Go) | Nombre de lignes par distribution | Nombre de lignes max par transaction |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4 000 000 | 240 000 000 |
| DW200 | 1\.5 | 60 | 90 | 6 000 000 | 360 000 000 |
| DW300 | 2\.25 | 60 | 135 | 9 000 000 | 540 000 000 |
| DW400 | 3 | 60 | 180 | 12 000 000 | 720 000 000 |
| DW500 | 3,75 | 60 | 225 | 15 000 000 | 900 000 000 |
| DW600 | 4\.5 | 60 | 270 | 18 000 000 | 1 080 000 000 |
| DW1000 | 7\.5 | 60 | 450 | 30 000 000 | 1 800 000 000 |
| DW1200 | 9 | 60 | 540 | 36 000 000 | 2 160 000 000 |
| DW1500 | 11,25 | 60 | 675 | 45 000 000 | 2 700 000 000 |
| DW2000 | 15 | 60 | 900 | 60 000 000 | 3 600 000 000 |
| DW3000 | 22,5 | 60 | 1 350 | 90 000 000 | 5 400 000 000 |
| DW6000 | 45 | 60 | 2 700 | 180 000 000 | 10 800 000 000 |

La limite de taille de transaction est appliquée par transaction ou opération. Elle n’est pas appliquée à toutes les transactions simultanées. Par conséquent, chaque transaction est autorisée à écrire cette quantité de données dans le journal.

Pour optimiser et réduire la quantité de données écrites dans le journal, consultez l’article sur les [bonnes pratiques relatives aux transactions][].

> [AZURE.WARNING] La taille de transaction maximale ne peut être obtenue que pour les tables distribuées HASH ou ROUND\_ROBIN où la répartition des données est égale. Si la transaction écrit les données de manière asymétrique dans les distributions, alors la limite est susceptible d’être atteinte avant la taille de transaction maximale.
<!--REPLICATED_TABLE-->

## État des transactions
SQL Data Warehouse utilise la fonction XACT\_STATE() pour signaler l’échec d’une transaction, en utilisant la valeur -2. Cette valeur signifie que la transaction a échoué et est marquée pour une restauration uniquement.

> [AZURE.NOTE] L’association de la valeur -2 à la fonction XACT\_STATE afin de signaler l’échec d’une transaction constitue un comportement différent par rapport à SQL Server. En effet, SQL Server utilise la valeur -1 pour indiquer qu’une transaction ne peut pas être validée. De plus, il peut tolérer la présence de certaines erreurs au sein d’une transaction sans pour autant signaler que cette dernière ne peut pas être validée. Par exemple, la valeur SELECT 1/0 entraîne une erreur, mais n’oblige pas la transaction à passer à l’état non validable. Par ailleurs, SQL Server autorise également les lectures dans une transaction non validable, ce que ne fait pas SQLDW. Si une erreur se produit dans une transaction SQLDW, cette dernière passe automatiquement à l’état -2, y compris les erreurs SELECT 1/0. Vous devez donc impérativement vérifier le code de votre application, afin de vous assurer qu’il utilise la fonction XACT\_STATE().

Dans SQL Server, vous pouvez voir apparaître un fragment de code ressemblant à ce qui suit :

```sql
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

```sql
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

## Fonction Error\_Line()
Il est également important de signaler que SQL Data Warehouse n’implémente pas et ne prend pas en charge la fonction ERROR\_LINE(). Si cette fonction est incluse dans votre code, vous devez la supprimer pour respecter les exigences de SQL Data Warehouse. Placez plutôt des libellés de requête dans votre code pour implémenter les fonctionnalités équivalentes. Consultez l’article relatif aux [libellés][] pour en savoir plus.

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
- Aucune prise en charge de DDL comme `CREATE TABLE` dans une transaction définie par l’utilisateur

## Étapes suivantes
Pour en savoir plus sur l’optimisation des transactions, consultez les [bonnes pratiques relatives aux transactions][]. Pour en savoir plus sur les bonnes pratiques relatives à SQL Data Warehouse, consultez l’article [correspondant][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[bonnes pratiques relatives aux transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[correspondant]: ./sql-data-warehouse-best-practices.md
[libellés]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0713_2016-->