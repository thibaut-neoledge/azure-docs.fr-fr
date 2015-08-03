<properties
   pageTitle="Planification pour la continuité des activités dans SQL Data Warehouse | Microsoft Azure "
   description="Vue d’ensemble de la continuité des activités dans SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="sahajs"/>


# Planification pour la continuité des activités dans SQL Data Warehouse

Cet article vous présente les bases de la planification de la continuité d’activité et de la récupération d’urgence avec SQL Data Warehouse. Les entrepôts de données sont les référentiels centraux des informations des entreprises, et ils jouent au quotidien un rôle critique dans les opérations analytiques et décisionnelles à tous les niveaux des structures. Par conséquent, il est essentiel que vous disposiez d’un entrepôt de données fiable, qui prenne en charge la récupération et la continuité des activités. Plus spécifiquement, cet article vous fera découvrir comment SQL Data Warehouse vous permet de récupérer après des erreurs d’utilisateurs et des sinistres à l’aide de la limite de restauration dans le temps et de la restauration géographique.

## Concepts

Les plans de continuité des activités et de récupération d’urgence doivent être optimisés dans les domaines suivants :

**Objectif de délai de récupération :** la durée maximale acceptable avant laquelle la base de données récupère intégralement après un événement perturbateur (perte maximale de disponibilité durant les défaillances).

**Objectif de point de récupération :** fenêtre maximale acceptable de mises à jour perdues lorsque la base de données récupère après un événement perturbateur (perte maximale de données durant les défaillances).

**Temps de récupération estimé :** durée estimée de rétablissement de la base de données après une requête de restauration.

## Scénarios de continuité des activités

**Récupération après des défaillances d’infrastructure :** ce scénario est relatif à la récupération après des défaillances d’infrastructure (défaillances de disques, etc.). Un client souhaite garantir la continuité des activités avec une infrastructure hautement disponible, à tolérance de pannes.

**Récupération après des erreurs d’utilisateurs :** ce scénario est relatif à la récupération après des corruptions ou suppressions accidentelles de données. Si un utilisateur modifie ou supprime accidentellement des données, un client souhaite garantir la disponibilité des activités en restaurant la base de données à un point antérieur dans le temps.

**Récupération d’urgence :** ce scénario est utilisé après une catastrophe majeure. Lorsqu’un événement comme une catastrophe naturelle ou une panne régionale rend la base de données indisponible, un client souhaite récupérer la base de données dans une région différente, afin de poursuivre ses activités.


## Fonctionnalités de continuité des activités

Penchons-nous sur la manière dont SQL Data Warehouse améliore la fiabilité de votre base de données et prend en charge les scénarios mentionnés plus haut récupération et de continuité des activités


### Redondance des données

Dans la mesure où SQL Data Warehouse sépare le calcul du stockage, l’ensemble de vos données sont directement écrites sur le stockage géo-redondant à accès en lecture de Microsoft Azure. Le stockage géo-redondant réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Au sein des régions principales et secondaires, vos données sont répliquées trois fois, dans les domaines d’erreur et les domaines de mise à niveau. Ainsi, vous êtes assuré que les données restent disponibles, même en cas de panne régionale intégrale ou de sinistre rendant l’une des régions indisponible. Pour en savoir plus sur le stockage géo-redondant avec accès en lecture, consultez la section [Options de redondance de stockage Microsoft Azure][].

### Restauration de base de données

Cette fonctionnalité est utilisée pour restaurer votre base de données à un point antérieur dans le temps. Le service Microsoft Azure SQL Data Warehouse protège l’ensemble des bases de données à l’aide d’instantanés automatiques du stockage pris toutes les 8 heures, qui sont conservés pendant 7 jours afin de vous offrir un ensemble discret de points de restauration. Ces sauvegardes sont stockées sur un espace de stockage géo-redondant avec accès en lecture Microsoft Azure (RA-GRS). Les fonctionnalités automatiques de sauvegarde et de restauration, qui sont proposées gratuitement, ne requièrent aucun coût d’administration pour protéger les bases de données d’une corruption ou d’une suppression accidentelles. Pour en savoir plus sur la restauration de base de données, consultez la section [Récupérer après une erreur d’utilisateur][].

### Restauration géographique

La restauration géographique est conçue pour prendre en charge la récupération d’une base de données rendue indisponible en raison d’un événement perturbateur. Pour restaurer une base de données à partir d’une sauvegarde géo-redondante et créer une base de données dans une autre région Azure, contactez le support. La sauvegarde étant géo-redondante, vous pouvez la mettre à profit pour récupérer une base de données qu’une panne a rendu inaccessible. La fonctionnalité de restauration géographique est proposée gratuitement.


## Étapes suivantes
Pour en savoir plus sur les fonctionnalités de continuité d’activité d’autres éditions de base de données SQL, consultez la page [Vue d’ensemble de la continuité des activités][].

<!--Image references-->

<!--Article references-->
[business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Options de redondance de stockage Microsoft Azure]: storage-redundancy/#read-access-geo-redundant-storage-ra-grs.md
[Vue d’ensemble de la continuité des activités]: ../sql-database/sql-database-business-continuity.md
[Récupérer après une erreur d’utilisateur]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=July15_HO4-->