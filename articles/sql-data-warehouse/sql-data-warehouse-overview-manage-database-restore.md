<properties
   pageTitle="Restauration d’une base de données dans Azure SQL Data Warehouse (Vue d’ensemble) | Microsoft Azure"
   description="Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Restauration d’une base de données dans Azure SQL Data Warehouse (Vue d’ensemble)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-manage-database-restore.md)
- [Portail](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Décrit les options de restauration d’une base de données dans Azure SQL Data Warehouse, notamment la restauration d’une base de données active, d’une base de données supprimée et d’une base de données inaccessible. Les bases de données actives et supprimées sont restaurées avec un instantané du même centre de données. La base de données inaccessible peut être déconnectée en raison d’une panne. Dans ce cas, la restauration s’effectue à partir d’un centre de données situé dans une autre région géographique.


## Scénarios de récupération

**Récupération après des défaillances d’infrastructure :** ce scénario est relatif à la récupération après des défaillances d’infrastructure (défaillances de disques, etc.). Un client souhaite garantir la continuité des activités avec une infrastructure hautement disponible, à tolérance de pannes.

**Récupération après des erreurs d’utilisateurs :** ce scénario est relatif à la récupération après des corruptions ou suppressions accidentelles de données. Si un utilisateur modifie ou supprime accidentellement des données, un client souhaite garantir la disponibilité des activités en restaurant la base de données à un point antérieur dans le temps.

**Récupération d’urgence :** ce scénario est utilisé après une catastrophe majeure. Lorsqu’un événement comme une catastrophe naturelle ou une panne régionale rend la base de données indisponible, un client souhaite récupérer la base de données dans une région différente, afin de poursuivre ses activités.

## Stratégies de capture d’instantanés

[AZURE.INCLUDE [Stratégie de rétention des sauvegardes SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Fonctionnalités de restauration de base de données

Penchons-nous sur la manière dont SQL Data Warehouse améliore la fiabilité de votre base de données et prend en charge les scénarios mentionnés plus haut récupération et de continuité des activités


### Redondance des données

SQL Data Warehouse stocke l’ensemble des données dans le système géo-redondant Azure Storage (RA-GRS). Le stockage géo-redondant réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Au sein des régions principales et secondaires, vos données sont répliquées trois fois, dans les domaines d’erreur et les domaines de mise à niveau. Ainsi, vous êtes assuré que les données restent disponibles, même en cas de panne régionale intégrale ou de sinistre rendant l’une des régions indisponible. Pour en savoir plus sur le stockage géo-redondant avec accès en lecture, consultez la section [Options de redondance de stockage Microsoft Azure][].

### Restauration de base de données

Cette fonctionnalité est utilisée pour restaurer votre base de données à un point antérieur dans le temps. Le service SQL Data Warehouse d’Azure protège toutes les bases de données avec des captures instantanées de stockage automatiques au moins toutes les 8 heures et les conserve pendant 7 jours afin de vous offrir un ensemble discret de points de restauration. Ces instantanés sont stockés sur RA-GRS Azure Storage et sont donc géo-redondants par défaut. Les fonctionnalités automatiques de capture d’instantanés et de restauration ne requièrent aucun coût d’administration pour protéger les bases de données d’une corruption ou d’une suppression accidentelle. Pour en savoir plus sur la restauration de base de données, consultez la section [Tâches de restauration de base de données][].

### Restauration géographique

La restauration géographique est conçue pour prendre en charge la récupération d’une base de données rendue indisponible en raison d’un événement perturbateur. La sauvegarde étant géo-redondante, vous pouvez la mettre à profit pour récupérer une base de données qu’une panne a rendu inaccessible. La base de données restaurée peut être créée sur n’importe quel serveur dans n’importe quelle région Azure. Outre la récupération après une défaillance, la géorestauration peut également servir pour d’autres scénarios, tels que la migration ou la copie d’une base de données vers un autre serveur ou une autre région.

**À quel moment initier la récupération** L’opération de récupération requiert la modification de la chaîne de connexion SQL et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement quand la défaillance est susceptible de durer plus longtemps que le RTO de votre application. Pour indiquer que la récupération est justifiée, utilisez les points de données suivants :

- Problème de connectivité permanent à la base de données.
- Votre portail Azure affiche une alerte concernant un incident dans la région avec un large impact.


## Étapes suivantes
Pour d’autres tâches de gestion significatives, consultez la [vue d’ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Options de redondance de stockage Microsoft Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[vue d’ensemble de la gestion]: sql-data-warehouse-overview-management.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->