---
title: "Restaurer un entrepôt de données Azure - local et géoredondant | Microsoft Docs"
description: "Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 104986e88ededf2137725fe258b6ce51f608b37d


---
# <a name="sql-data-warehouse-restore"></a>Restauration SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Vue d’ensemble][Overview]
> * [Portail][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse propose des restaurations locales et géographiques dans le cadre de ses fonctionnalités de récupération d’urgence. Utilisez des sauvegardes d’entrepôt de données pour restaurer votre entrepôt de données à un point de restauration dans la région primaire, ou des sauvegardes géo-redondantes pour restaurer dans une autre région géographique. Cet article explique les spécificités de la restauration d’un entrepôt de données.

## <a name="what-is-a-data-warehouse-restore"></a>Qu’est-ce qu’une restauration d’entrepôt de données ?
Une restauration d’entrepôt de données est un nouvel entrepôt de données créé à partir de la sauvegarde d’un entrepôt de données existant ou supprimé. L’entrepôt de données restauré recrée l’entrepôt de données sauvegardé à un moment donné. Comme SQL Data Warehouse est un système distribué, une restauration d’entrepôt de données est créée à partir de nombreux fichiers de sauvegarde stockés dans des objets blob Azure. 

La restauration de base de données est une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elle recrée vos données après des corruptions et des suppressions accidentelles.

Pour plus d'informations, consultez les pages suivantes :

* [Sauvegardes SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Vue d’ensemble de la continuité des activités](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Points de restauration SQL Data Warehouse
Un avantage de l’utilisation de Stockage Premium Azure est que SQL Data Warehouse utilise des captures instantanées d’objet blob Stockage Azure pour sauvegarder l’entrepôt de données principal. Chaque capture instantanée a un point de restauration qui représente son heure de début. Pour restaurer un entrepôt de données, choisissez un point de restauration et émettez une commande de restauration.  

SQL Data Warehouse restaure toujours la sauvegarde vers un nouvel entrepôt de données. Vous pouvez conserver les entrepôts de données restauré et actuel ou les supprimer tous les deux. Si vous souhaitez remplacer l’entrepôt de données en cours par l’entrepôt de données restauré, vous pouvez le renommer.

Si vous devez restaurer un entrepôt de données supprimé ou en pause, vous pouvez [créer un ticket de support](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restauration géoredondante
Si vous utilisez le stockage géoredondant, vous pouvez restaurer l’entrepôt de données sur votre [centre de données couplé](../best-practices-availability-paired-regions.md) dans une autre région géographique. L’entrepôt de données est restauré à partir de la dernière sauvegarde quotidienne. 

## <a name="restore-timeline"></a>Chronologie de la restauration
Vous pouvez restaurer une base de données à un point de restauration disponible des sept derniers jours. Les captures instantanées démarrent toutes les quatre à huit heures et sont disponibles pendant sept jours. Quand une capture instantanée a une ancienneté supérieure à sept jours, elle expire et son point de restauration n’est plus disponible.

## <a name="restore-costs"></a>Coûts de la restauration
Les frais de stockage pour l’entrepôt de données restauré sont facturés au tarif du Stockage Premium Azure. 

Si vous mettez en suspens un entrepôt de données restauré, vous êtes facturé pour le stockage au tarif du Stockage Premium Azure. L’avantage de la mise en suspens est que vous n’êtes pas facturé pour les ressources de calcul DWU.

Pour plus d’informations sur la tarification de SQL Data Warehouse, consultez [SQL Data Warehouse Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Utilisations de la restauration
La restauration de l’entrepôt de données permet principalement de récupérer des données après une corruption ou une perte accidentelle.

Elle permet également de conserver une sauvegarde pendant plus de sept jours. Une fois que la sauvegarde est restaurée, l’entrepôt de données est en ligne et vous pouvez le suspendre indéfiniment pour réduire les coûts de calcul. La base de données en pause entraîne des frais de stockage aux tarifs du Stockage Premium Azure. 

## <a name="related-topics"></a>Rubriques connexes
### <a name="scenarios"></a>Scénarios
* Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Pour effectuer une restauration d’entrepôt de données, utilisez l’une des méthodes suivantes :

* Portail Azure : voir [Restaurer un entrepôt de données à l’aide du portail Azure](sql-data-warehouse-restore-database-portal.md).
* Applets de commande PowerShell : voir [Restaurer un entrepôt de données à l’aide d’applets de commande PowerShell](sql-data-warehouse-restore-database-powershell.md)
* API REST : voir [Restaurer un entrepôt de données à l’aide d’API REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Jan17_HO4-->


