<properties
   pageTitle="Changements de noms dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la modification de noms de tables dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="01/04/2016"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# Changements de noms dans SQL Data Warehouse
SQL Server prend en charge le changement de nom de base de données à l'aide de la procédure stockée ```sp_renamedb```. Pour atteindre cet objectif, SQL Data Warehouse utilise quant à lui la syntaxe DDL. La commande DDL est ```RENAME OBJECT```.

## Changement de nom de table

Actuellement, seules les tables peuvent être renommées. La syntaxe permettant de renommer une table est la suivante :

```
RENAME OBJECT Customer TO NewCustomer;
```

Lorsque vous renommez une table, tous les objets et les propriétés associés à cette table sont mises à jour pour référencer le nouveau nom de table. Par exemple, les définitions de table, les index, les contraintes et les autorisations sont mis à jour. Les vues ne sont pas mises à jour.

## Changement de nom de table externe

La modification du nom d'une table externe modifie le nom de la table dans SQL Server PDW. Elle n'affecte pas l'emplacement des données externes pour la table.

## Modification d'un schéma de table
Si vous voulez modifier le schéma auquel un objet appartient, vous pouvez utiliser MODIFIER LE SCHÉMA :

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Le changement de nom de table requiert un verrouillage exclusif de la table

N'oubliez pas qu'il est impossible de renommer une table en cours d'utilisation. Le changement de nom d'une table requiert un verrouillage exclusif de la table. Si la table est en cours d'utilisation, vous devrez peut-être fermer la session utilisant la table. Pour arrêter une session, vous devez utiliser la commande [KILL](https://msdn.microsoft.com/library/ms173730.aspx). N'oubliez pas que, lorsque vous utilisez ```KILL```, toute tâche non validée sera annulée si une session est fermée. Dans SQL Data Warehouse, les sessions se voient octroyer le préfixe « SID ». Vous devez l'inclure, ainsi que le numéro de session, lorsque vous appelez la commande KILL. Par exemple, ```KILL 'SID1234'```


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!---HONumber=AcomDC_0107_2016-->