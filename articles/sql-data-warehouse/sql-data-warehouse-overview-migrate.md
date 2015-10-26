<properties
   pageTitle="Migration de votre solution vers SQL Data Warehouse | Microsoft Azure"
   description="Recommandations relatives à la migration de votre solution vers la plateforme Microsoft Azure SQL Data Warehouse"
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

# Migration de votre solution vers SQL Data Warehouse

SQL Data Warehouse est un système de base de données distribuée qui se met à l’échelle de manière flexible afin de répondre à vos besoins. Pour maintenir des niveaux appropriés de performances et de mise à l’échelle, toutes les fonctionnalités de SQL Server ne sont pas implémentées dans SQL Data Warehouse. Les rubriques suivantes sur la migration évoquent certains des facteurs clés relatifs à la migration de votre solution vers SQL Data Warehouse. Le développement d’entrepôts de données prenant en charge la mise à l’échelle introduisant différents modèles de conception, les approches traditionnelles ne sont pas toujours les plus indiquées. Vous constaterez probablement qu’une adaptation de votre solution est nécessaire à la pleine valorisation de la plateforme distribuée fournie par SQL Data Warehouse.

Il est également important de se rappeler de la nature de SQL Data Warehouse, qui est une plateforme basée sur Microsoft Azure. Par conséquent, il est possible qu’une portion de votre migration implique le transfert vers le Cloud de vos données. Le transfert de données est une problématique à part entière, qui doit être étudiée avec soin, plus particulièrement lorsque les volumes augmentent. Par ailleurs, le transfert de données ne doit pas être confondu avec le chargement de données, qui fait l’objet d’une rubrique distincte.

## Recommandations en matière de migration
Avant d’amorcer votre migration, prenez le temps de lire ces articles afin de mieux comprendre certaines des différences entre les produits et les concepts fondamentaux.

- [Migration de votre schéma][]
- [Migration de vos données][]
- [Migration de votre code][]
 
## Étapes suivantes
Pour obtenir des conseils sur le développement, consultez la [vue d’ensemble sur le développement][].

Pour en savoir plus, vous pouvez également consulter la [documentation de référence sur Transact-SQL][].

Enfin, consultez la [vue d’ensemble sur le chargement][] qui présente différentes options de chargement de données et vous communique des recommandations étape par étape.

<!--Image references-->

<!--Article references-->
[Migration de votre schéma]: sql-data-warehouse-migrate-schema.md
[Migration de vos données]: sql-data-warehouse-migrate-data.md
[Migration de votre code]: sql-data-warehouse-migrate-code.md

[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md
[vue d’ensemble sur le chargement]: sql-data-warehouse-overview-load.md
[documentation de référence sur Transact-SQL]: sql-data-warehouse-overview-migrate.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=Oct15_HO3-->