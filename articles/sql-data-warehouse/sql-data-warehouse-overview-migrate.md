---
title: Migration de votre solution vers SQL Data Warehouse | Microsoft Docs
description: "Recommandations relatives à la migration de votre solution vers la plateforme Microsoft Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/27/2017
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 771b9456e66b8a1e41f72340b695b19e2adaf793
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrer votre solution vers Azure SQL Data Warehouse
Consultez les conséquences de la migration d’une solution de base de données existante vers Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profiler votre charge de travail
Avant d’amorcer votre migration, vérifiez que SQL Data Warehouse est la meilleure solution pour votre charge de travail. SQL Data Warehouse est système distribué conçu pour réaliser des analyses sur de grandes quantités de données.  La migration vers SQL Data Warehouse exige d’apporter quelques modifications à la conception. Ces modifications ne sont pas difficiles à comprendre, mais leur implémentation peut prendre du temps. Si votre entreprise requiert un entrepôt de données de niveau entreprise, les avantages obtenus en valent la peine. Toutefois, si vous n’avez pas besoin de la puissance de SQL Data Warehouse, il est plus rentable d’utiliser SQL Server ou Azure SQL Database.

Envisagez d’utiliser SQL Data Warehouse lorsque vous :
- Possédez plusieurs To de données
- Envisagez de réaliser des analyses sur de grandes quantités de données
- Devez être capable de mettre à l’échelle les calculs et le stockage 
- Souhaitez réduire les coûts en interrompant les ressources de calculs, si vous n’en avez pas besoin.

N’utilisez pas SQL Data Warehouse pour des charges de travail opérationnelles (OLTP) qui possèdent :
- Des lectures et écritures haute fréquence
- Un grand nombre de sélections singleton
- Des volumes élevés d’insertions à une seule ligne
- Des besoins de traitement ligne par ligne
- Des formats incompatibles (JSON, XML)


## <a name="plan-the-migration"></a>Planifier la migration

Une fois que vous avez décidé de migrer une solution vers SQL Data Warehouse, il est important de planifier la migration avant de commencer. 

Un objectif de la planification est de vérifier que vos données, vos schémas de table et votre code sont compatibles avec SQL Data Warehouse. Entre votre système actuel et SQL Data Warehouse, il existe certaines différences de compatibilité à contourner. De plus, migrer d’importantes quantités de données vers Azure prend du temps. Une planification minutieuse accélère la migration de vos données vers Azure. 

Un autre objectif de la planification consiste à effectuer des ajustements de conception pour être certain que votre solution tire parti du niveau performances élevé des requêtes, fournies par SQL Data Warehouse. Le développement d’entrepôts de données prenant en charge la mise à l’échelle introduisant différents modèles de conception, les approches traditionnelles ne sont pas toujours les plus indiquées. Même si vous avez la possibilité d’apporter certaines modifications au niveau de la conception après la migration, apporter des modifications plus tôt dans le processus vous fera par la suite gagner du temps.

Pour réaliser une migration avec succès, vous devez migrer vos schémas de table, votre code et vos données. Lisez ces articles pour en apprendre davantage sur la migration :

-  [Migration de vos schémas](sql-data-warehouse-migrate-schema.md)
-  [Migration de votre code](sql-data-warehouse-migrate-code.md)
-  [Migration de vos données](sql-data-warehouse-migrate-data.md) 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Étapes suivantes
L’équipe de conseils à la clientèle (CAT, Customer Advisory Team) peut également fournir quelques recommandations sur SQL Data Warehouse, qu’elle publie en tant que billets de blog.  Consultez son article [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] pour obtenir des conseils supplémentaires sur la migration.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

