<properties
   pageTitle="Changements de noms dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la modification de noms de tables dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Changements de noms dans SQL Data Warehouse
SQL Data Warehouse utilise l’instruction [RENAME][] pour renommer des tables. Cela diffère de SQL Server, qui utilise `sp_rename`. Actuellement, seules les tables utilisateur peuvent être renommées. Les bases de données et tables externes ne peuvent pas être renommées.

## Changement de nom de table

Lorsque vous renommez une table, tous les objets et les propriétés associés à cette table sont mises à jour pour référencer le nouveau nom de table. Par exemple, les définitions de table, les index, les contraintes et les autorisations sont mis à jour. Les vues ne sont pas mises à jour.

La syntaxe permettant de renommer une table est la suivante :

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## Modification d'un schéma de table

Pour modifier le schéma auquel un objet appartient, utilisez ALTER SCHEMA :

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Le changement de nom de table requiert un verrouillage exclusif de la table

Vous ne pouvez pas renommer une table en cours d’utilisation. Le changement de nom d'une table requiert un verrouillage exclusif de la table. Si la table est en cours d'utilisation, vous devrez peut-être fermer la session utilisant la table. Utilisez la commande [KILL][] pour arrêter une session. Par exemple, `KILL 'SID1234'`. Utilisez KILL avec prudence, car toute tâche transactionnelle non validée sera annulée lors de l’arrêt de la session. Consultez l’article sur les connexions pour plus d’informations sur les [sessions et les requêtes][]. Consultez [Optimisation des transactions pour SQL Data Warehouse][] pour plus d’informations sur l’impact de l’arrêt d’une requête transactionnelle et l’effet d’une restauration.


## Étapes suivantes
Pour plus de références sur T-SQL, consultez les [références pour T-SQL][].

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[sessions et les requêtes]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[références pour T-SQL]: ./sql-data-warehouse-reference-tsql-statements.md
[Optimisation des transactions pour SQL Data Warehouse]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->