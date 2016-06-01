<properties
   pageTitle="Sauvegarde et restauration d’une base de données dans Azure SQL Data Warehouse (Vue d’ensemble) | Microsoft Azure"
   description="Vue d’ensemble des options de sauvegarde et de restauration pour la récupération d’une base de données dans Azure SQL Data Warehouse."
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


# Sauvegarde et restauration d’une base de données dans Azure SQL Data Warehouse (Vue d’ensemble)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portail](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Cet article vous présente les bases de la planification de la continuité d’activité et de la récupération d’urgence avec SQL Data Warehouse. Les entrepôts de données sont les référentiels centraux des informations des entreprises, et ils jouent au quotidien un rôle critique dans les opérations analytiques et décisionnelles à tous les niveaux des structures. Par conséquent, il est essentiel que vous disposiez d’un entrepôt de données fiable, qui prenne en charge la récupération et la continuité des activités. Plus spécifiquement, cet article vous fera découvrir comment SQL Data Warehouse permet de récupérer après des erreurs d’utilisateurs et des sinistres à l’aide de la restauration de base de données et de la restauration géographique.

## Planification de la récupération 

Les plans de continuité des activités et de récupération d’urgence doivent être optimisés dans les domaines suivants :

**Objectif de délai de récupération :** la durée maximale acceptable avant laquelle la base de données récupère intégralement après un événement perturbateur (perte maximale de disponibilité durant les défaillances).

**Objectif de point de récupération :** fenêtre maximale acceptable de mises à jour perdues lorsque la base de données récupère après un événement perturbateur (perte maximale de données durant les défaillances).

**Temps de récupération estimé :** durée estimée de rétablissement de la base de données après une requête de restauration.

Pour plus d’informations, consultez [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

## Scénarios de récupération

**Récupération après des défaillances d’infrastructure :** ce scénario est relatif à la récupération après des défaillances d’infrastructure (défaillances de disques, etc.). Un client souhaite garantir la continuité des activités avec une infrastructure hautement disponible, à tolérance de pannes.

**Récupération après des erreurs d’utilisateurs :** ce scénario est relatif à la récupération après des corruptions ou suppressions accidentelles de données. Si un utilisateur modifie ou supprime accidentellement des données, un client souhaite garantir la disponibilité des activités en restaurant la base de données à un point antérieur dans le temps.

**Récupération d’urgence :** ce scénario est utilisé après une catastrophe majeure. Lorsqu’un événement comme une catastrophe naturelle ou une panne régionale rend la base de données indisponible, un client souhaite récupérer la base de données dans une région différente, afin de poursuivre ses activités.


## Fonctionnalités de sauvegarde et de restauration

Penchons-nous sur la manière dont SQL Data Warehouse améliore la fiabilité de votre base de données et prend en charge les scénarios mentionnés plus haut récupération et de continuité des activités


### Redondance des données

SQL Data Warehouse distingue le calcul du stockage. Toutes vos données sont directement écrites dans le stockage Azure géo-redondant (RA-GRS). Le stockage géo-redondant réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Au sein des régions principales et secondaires, vos données sont répliquées trois fois, dans les domaines d’erreur et les domaines de mise à niveau. Ainsi, vous êtes assuré que les données restent disponibles, même en cas de panne régionale intégrale ou de sinistre rendant l’une des régions indisponible. Pour en savoir plus sur le stockage géo-redondant avec accès en lecture, consultez la section [Options de redondance de stockage Microsoft Azure][].

### Restauration de base de données

Cette fonctionnalité est utilisée pour restaurer votre base de données à un point antérieur dans le temps. Le service SQL Data Warehouse d’Azure protège toutes les bases de données avec des captures instantanées de stockage automatiques au moins toutes les 8 heures et les conserve pendant 7 jours afin de vous offrir un ensemble discret de points de restauration. Ces sauvegardes sont stockées sur un espace de stockage géo-redondant avec accès en lecture Microsoft Azure (RA-GRS). Les fonctionnalités automatiques de sauvegarde et de restauration ne requièrent aucun coût d’administration pour protéger les bases de données d’une corruption ou d’une suppression accidentelle. Pour en savoir plus sur la restauration de base de données, consultez la section [Tâches de sauvegarde et restauration][].

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
[Tâches de sauvegarde et restauration]: sql-data-warehouse-backup-and-restore-tasks-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[vue d’ensemble de la gestion]: sql-data-warehouse-overview-management.md
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ../sql-database/sql-database-business-continuity.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->