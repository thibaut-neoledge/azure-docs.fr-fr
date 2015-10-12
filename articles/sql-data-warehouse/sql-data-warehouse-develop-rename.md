<properties
   pageTitle="Changements de noms dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs au changement de nom d’un objet ou d’une base de données dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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

# Changements de noms dans SQL Data Warehouse
SQL Server prend en charge les changements de noms d’objets et de bases de données via les procédures stockées « sp\_rename » et « sp\_renamedb », respectivement.

Pour atteindre cet objectif, le logiciel SQL Data Warehouse utilise quant à lui la syntaxe DDL. Les commandes DDL sont RENAME OBJECT et RENAME DATABASE.

## Renommer un objet

Il est important de comprendre que le changement de nom porte uniquement sur l’objet concerné ; cette opération n’est pas propagée. Par conséquent, toutes les vues qui utilisent l’ancien nom d’un objet resteront non valides tant qu’un objet présentant l’ancien nom ne sera pas créé. De ce fait, vous verrez souvent le paramètre `RENAME OBJECT` apparaître par paires.

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## Renommer une base de données

Le changement de nom d’une base de données est très similaire à celui d’un objet.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

N’oubliez pas que vous ne pouvez pas renommer un objet ou une base de données si d’autres utilisateurs sont connectés à ces derniers ou en tirent parti. Vous devrez peut-être arrêter toutes les sessions ouvertes au préalable. Pour arrêter une session, vous devez utiliser la commande [KILL][]. Soyez prudent lorsque vous l’utilisez. Une fois cette commande exécutée, la session ciblée est arrêtée ; toute tâche non validée est annulée.

> [AZURE.NOTE]Dans SQL Data Warehouse, les sessions se voient octroyer le préfixe « SID ». Vous devez l’inclure, ainsi que le numéro de session, lorsque vous appelez la commande KILL. Par exemple, la commande KILL 'SID1234' arrête la session 1234 (il faut cependant que vous disposiez des autorisations requises).

## Exécutions de commandes KILL sur des sessions
Pour renommer une base de données, vous devrez peut-être arrêter les sessions connectées à votre instance SQL Data Warehouse. La requête suivante génère une liste distincte de commandes KILL pour effacer les connexions (sauf pour la session en cours).

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
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

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/fr-FR/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=Oct15_HO1-->