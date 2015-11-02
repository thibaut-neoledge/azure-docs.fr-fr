<properties
   pageTitle="Changements de noms dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs au changement de nom d’un objet ou d’une base de données dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# Changements de noms dans SQL Data Warehouse
SQL Server prend en charge le changement de nom de base de données via la procédure stockée ```sp_renamedb```. Pour atteindre cet objectif, le logiciel SQL Data Warehouse utilise quant à lui la syntaxe DDL. La commande DDL est ```RENAME DATABASE```.

## Renommer une base de données

Pour renommer une base de données, exécutez la commande suivante.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

N’oubliez pas que vous ne pouvez pas renommer un objet ou une base de données si d’autres utilisateurs sont connectés à ces derniers ou en tirent parti. Vous devrez peut-être arrêter toutes les sessions ouvertes au préalable. Pour arrêter une session, vous devez utiliser la commande [KILL](https://msdn.microsoft.com/library/ms173730.aspx). Soyez prudent quand vous utilisez ```KILL```. Une fois cette commande exécutée, la session ciblée est arrêtée ; toute tâche non validée est annulée.

> [AZURE.NOTE]Dans SQL Data Warehouse, les sessions se voient octroyer le préfixe « SID ». Vous devez l’inclure, ainsi que le numéro de session, lorsque vous appelez la commande KILL. Par exemple, la commande ```KILL 'SID1234'``` arrête la session 1234 (il faut cependant que vous disposiez des autorisations requises).

## Exécutions de commandes KILL sur des sessions
Pour renommer une base de données, vous devrez peut-être arrêter les sessions connectées à votre instance SQL Data Warehouse. La requête suivante génère une liste distincte de commandes KILL pour effacer les connexions (sauf pour la session en cours).

```
SELECT
    'KILL ''' + session_id + ''''
FROM
    sys.dm_pdw_exec_requests r
WHERE
    r.session_id <> SESSION_ID()
    AND EXISTS
    (
        SELECT
            *
        FROM
            sys.dm_pdw_lock_waits w
        WHERE
            r.session_id = w.session_id
    )
GROUP BY
    session_id;
```

## Basculement de schémas
Si vous voulez modifier le schéma auquel appartient un objet, vous pouvez utiliser `ALTER SCHEMA` :

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!---HONumber=Oct15_HO4-->