---
title: "Géo-réplication active pour la base de données SQL Azure"
description: "La géo-réplication active permet de configurer 4 réplicas de votre base de données dans un des centres de données Azure."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/26/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: bd3aea04266baebbba1b953d5a2b7c4b2fb41a87
ms.lasthandoff: 03/28/2017


---
# <a name="overview-sql-database-active-geo-replication"></a>Vue d’ensemble : Géo-réplication active de base de données SQL
La géo-réplication active permet de configurer jusqu'à quatre bases de données secondaires accessibles en lecture dans des emplacements de centres de données identiques ou différents (régions). Les bases de données secondaires sont disponibles pour l’interrogation et le basculement en cas d’indisponibilité d’un centre de données ou l’incapacité à se connecter à la base de données primaire. La géo-réplication active doit être entre des bases de données au sein du même abonnement.

> [!NOTE]
> La géo-réplication active (bases de données secondaires accessibles en lecture) est désormais disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 sera retiré le type secondaire non accessible en lecture et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture.
>  

 Vous pouvez configurer la géo-réplication active à l’aide du [portail Azure](sql-database-geo-replication-portal.md), de [PowerShell](sql-database-geo-replication-powershell.md), de [Transact-SQL](sql-database-geo-replication-transact-sql.md) ou de [l’API REST - Créer ou mettre à jour la base de données](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Si, pour une raison quelconque, votre base de données primaire échoue ou doit simplement être mise hors connexion, vous pouvez effectuer un *basculement* vers l’une de vos bases de données secondaires. Lorsque le basculement est activé pour l’une des bases de données secondaires, toutes les autres bases de données secondaires sont automatiquement liées à la nouvelle base de données primaire.

Vous pouvez basculer vers une base de données secondaire en utilisant le [portail Azure](sql-database-geo-replication-failover-portal.md), [PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), [l’API REST - Basculement planifié](https://msdn.microsoft.com/library/mt575007.aspx) ou [l’API REST - Basculement non planifié](https://msdn.microsoft.com/library/mt582027.aspx).

Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de votre base de données sont configurées sur la nouvelle base de données primaire. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).

La fonctionnalité de géo-réplication active implémente un mécanisme pour assurer la redondance de base de données dans une même région Microsoft Azure ou dans différentes régions (géo-redondance). La géoréplication active réplique de manière asynchrone les transactions validées à partir d’une base de données sur jusqu’à quatre copies de la base de données sur des serveurs différents à l’aide de l’isolement de capture instantanée à lecture validée (RCSI). Lorsque la géo-réplication active est configurée, une base de données secondaire est créée sur le serveur spécifié. La base de données d’origine devient la base de données primaire. La base de données primaire réplique de manière asynchrone les transactions validées sur chaque base de données secondaire. Seules les transactions complètes sont répliquées. À un moment donné, la base de données secondaire peut être légèrement en retard sur la base de données primaire, mais les données secondaire ne peuvent jamais contenir de transactions partielles. Vous trouverez les données d’objectif de point de récupération dans [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

L’un des principaux avantages de la géo-réplication active est qu’elle fournit une solution de récupération d’urgence au niveau de la base de données, avec temps de récupération court. En plaçant la base de données secondaire sur un serveur situé dans une autre région, vous ajoutez une tolérance maximale à votre application. Grâce à la redondance entre régions, les applications peuvent récupérer d’une perte permanente de tout ou partie d’un centre de données résultant de catastrophes naturelles, de graves erreurs humaines ou d’actes de malveillance. La figure suivante présente un exemple de géoréplication active configurée avec une base de données primaire située dans le Nord du centre des États-Unis, et une base de données secondaire située dans la région Sud du centre des États-Unis.

![Relation de géoréplication](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Un autre avantage clé est que les bases de données secondaires sont lisibles et peuvent être utilisées pour décharger des charges de travail en lecture seule, telles que des travaux de génération de rapports. Si vous souhaitez utiliser la base de données secondaire uniquement pour l’équilibrage de charge, vous pouvez la créer dans la même région que la base de données primaire. La création d’une base de données secondaire n’augmente pas la tolérance de l’application aux défaillances catastrophiques.  

Autres scénarios dans lesquels la géo-réplication active peut être utilisée :

* **Migration de base de données**: la géo-réplication active permet de migrer une base de données d’un serveur à un autre serveur en ligne avec un temps d’arrêt minimal.
* **Mises à niveau de l’application**: vous pouvez créer une base de données secondaire supplémentaire faisant office de copie de restauration automatique lors des mises à niveau de l’application.

Pour assurer vraiment la continuité des activités, l’ajout d’une redondance de base de données entre les centres de données n’est qu’une partie de la solution. La récupération d’une application (service) de bout en bout après une défaillance catastrophique implique la récupération de tous les composants constituant le service et tous les services dépendants. En voici quelques exemples : logiciel client (il peut s’agir par exemple d’un navigateur avec un code JavaScript personnalisé), serveurs web frontaux, ressources de stockage et DNS. Il est essentiel que tous les composants résistent aux mêmes défaillances et redeviennent disponibles dans l’objectif de délai de récupération (RTO) de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre les mesures appropriées pour vous assurer que votre service fonctionne pendant le basculement des services dont il dépend. Pour plus d’informations sur la conception de solutions pour la récupération d’urgence, consultez la section [Conception de solutions cloud pour la récupération d’urgence à l’aide de la géo-réplication active](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Fonctionnalités de géo-réplication active
La fonctionnalité de géo-réplication active fournit les capacités essentielles suivantes :

* **Réplication asynchrone automatique**: vous pouvez créer une base de données secondaire uniquement par un ajout à une base de données existante. La base de données secondaire peut être créée uniquement sur un autre serveur de base de données SQL Azure. Une fois créée, la base de données secondaire est remplie avec les données copiées à partir de la base de données primaire. Ce processus est appelé amorçage. Une fois la base de données secondaire créée et amorcée, les mises à jour de la base de données primaire sont automatiquement répliquées de manière asynchrone sur la base de données secondaire. La réplication asynchrone signifie que les transactions sont validées sur la base de données primaire avant leur réplication sur la base de données secondaire. 
* **Plusieurs bases de données secondaires**: deux bases de données secondaires au minimum améliorent la redondance et le niveau de protection de la base de données primaire et de l’application. S’il existe plusieurs bases de données secondaires, l’application reste protégée même en cas d’échec de l’une des bases de données secondaires. S’il n’existe qu’une seule base de données secondaire, en cas d’échec de celle-ci, l’application est exposée à un risque plus élevé jusqu’à la création d’une nouvelle base de données secondaire.
* **Bases de données secondaires accessibles en lecture**: une application peut accéder à une base de données secondaire pour des opérations en lecture seule avec des entités de sécurité qui peuvent différer de celles utilisées pour accéder à la base de données primaire. Les bases de données secondaires fonctionnent en mode d’isolement d’instantané pour garantir que la réplication des mises à jour de la base de données primaire n’est pas retardée par des requêtes exécutées sur la base de données secondaire.

> [!NOTE]
> La relecture du journal est retardée sur la base de données secondaire si celle-ci reçoit de la base de données primaire des mises à jour de schéma, car cette opération nécessite un verrou de schéma sur la base de données secondaire.
> 
> 

* **Géo-réplication active de bases de données de pool élastique** : une géo-réplication active peut être configurée pour toute base de données faisant partie d’un pool élastique. La base de données secondaire peut se trouver dans un autre pool élastique. Pour des bases de données ordinaires, la base de données secondaire peut être un pool élastiques, et inversement, pour autant que les niveaux de service soient identiques. 
* **Niveau de performances configurable de la base de données secondaire**: une base de données secondaire peut être créée avec un niveau de performances inférieur à celui de la base de données primaire. Les bases de données primaire et secondaire doivent offrir le même niveau de service. Cette option n’est pas recommandée pour les applications dont l’activité d’écriture de base de données est importante, car elle risque d’entraîner un décalage de réplication accru et, par conséquent, d’exposer à un risque élevé de perte substantielle de données après basculement. En outre, après basculement, les performances de l’application sont affectées jusqu’à ce que la nouvelle base de données primaire soit mise à niveau vers un niveau de performances supérieur. Le graphique de pourcentage d’E/S du journal sur le portail Azure permet d’estimer le niveau de performances minimal de la base de données secondaire requis pour supporter la charge de réplication. Par exemple, si votre base de données primaire est P6 (1 000 DTU) et si son pourcentage d’E/S du journal est de 50 %, la base de données secondaire doit être au moins P4 (500 DTU). Vous pouvez également récupérer les données d’E/S du journal à l’aide des vues de base de données [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ou [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).  Pour plus d’informations sur les niveaux de performances des bases de données SQL, voir [Options et performances de la base de données SQL](sql-database-service-tiers.md). 
* **Basculement et restauration automatique contrôlés par l’utilisateur**: une base de données secondaire peut être basculée à tout moment vers le rôle primaire sur une action explicite de l’application ou de l’utilisateur. Pendant une panne réelle, l’option « non planifiée » doit être utilisée, qui promeut immédiatement une base de données secondaire en base de données primaire. Lorsque la base de données primaire en échec récupère et redevient disponible, le système marque automatiquement la base de données primaire récupérée comme base de données secondaire, et la met à jour par rapport à la nouvelle base de données primaire. En raison de la nature asynchrone de la réplication, une petite quantité de données peut être perdue lors de basculements non planifiés si une base de données primaire échoue avant la réplication des modifications les plus récentes sur la base de données secondaire. Quand une base de données primaire avec plusieurs bases de données secondaires bascule, le système reconfigure automatiquement les relations de réplication et lie les bases de données secondaires restantes à la base de données nouvellement promue comme primaire sans aucune intervention de l’utilisateur. Une fois la panne à l’origine du basculement résolue, il peut être judicieux de rétablir l’application dans la région primaire. Pour ce faire, la commande de basculement doit être appelée avec l’option « planifiée ». 
* **Synchronisation des informations d’identification et des règles de pare-feu** : nous recommandons d’utiliser des [règles de pare-feu de base de données](sql-database-firewall-configure.md) pour les bases de données géorépliquées de façon à ce que ces règles puissent être répliquées avec la base de données pour s’assurer que toutes les bases de données secondaires ont les mêmes règles de pare-feu que la base de données primaire. Cette approche évite aux clients de devoir configurer et tenir à jour manuellement les règles de pare-feu sur les serveurs hébergeant les bases de données primaire et secondaires. De même, le recours à des [utilisateurs de base de données contenus](sql-database-manage-logins.md) pour l’accès aux données garantit que les bases de données primaires et secondaires ont toujours les mêmes informations d’identification d’utilisateur afin qu’un basculement n’entraîne aucune interruption due à une discordance d’ID de connexion et de mots de passe. Avec l’ajout [d’Azure Active Directory](../active-directory/active-directory-whatis.md), les clients peuvent gérer l’accès utilisateur aux bases de données primaires et secondaires. Cela élimine également la nécessité de gérer les informations d’identification dans l’ensemble des bases de données.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Mise à niveau ou rétrogradation d’une base de données primaire
Vous pouvez mettre à niveau ou rétrograder une base de données primaire à un niveau de performance différent (avec le même niveau de service) sans déconnecter les bases de données secondaires. Lors d’une mise à niveau, nous vous recommandons de mettre à niveau la base de données secondaire dans un premier temps, avant de mettre à niveau la base de données primaire. Lors d’une rétrogradation, inversez l’ordre : rétrogradez tout d’abord la base de données primaire, puis dans un second temps la base de données secondaire. 

La base de données secondaire devant se trouver dans le même niveau de service que la base de données primaire, la migration de votre base de données primaire vers un niveau de service différent vous oblige à mettre fin à la liaison de géoréplication et à renommer la base de données secondaire, ou à l’abandonner. Ensuite, migrez la base de données primaire vers le nouveau niveau de service, puis reconfigurez la géoréplication. Votre nouvelle base de données sera créée automatiquement avec le même niveau de performance que la base de données primaire.

## <a name="preventing-the-loss-of-critical-data"></a>Prévention de la perte de données critiques
En raison de la latence élevée des réseaux étendus, la copie continue utilise un mécanisme de réplication asynchrone. La perte de certaines données reste donc inévitable en cas de défaillance. Or, pour certaines applications, une perte de données est inacceptable. Pour protéger ces mises à jour critiques, un développeur d’applications peut appeler la procédure système [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) immédiatement après la validation de la transaction. L’appel de **sp_wait_for_database_copy_sync** bloque le thread appelant jusqu’à ce que la dernière transaction validée ait été répliquée sur la base de données secondaire. La procédure attend que toutes les transactions en file d’attente aient été acceptées par la base de données secondaire. **sp_wait_for_database_copy_sync** est limité à une relation de copie continue spécifique. Tout utilisateur disposant de droits de connexion à la base de données primaire peut appeler cette procédure.

> [!NOTE]
> Le délai provoqué par un appel de procédure **sp_wait_for_database_copy_sync** peut être significatif. Le délai dépend de la longueur du journal des transactions à ce moment, et cet appel n’est pas retourné tant que la totalité du journal n’est pas répliquée. Évitez d’appeler cette procédure, sauf si cela est absolument nécessaire.
> 
> 

## <a name="programmatically-managing-active-geo-replication"></a>Gestion de la géo-réplication active par programme
Comme indiqué plus haut, la géo-réplication active peut aussi être gérée par programme à l’aide d’Azure PowerShell et de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

* **API Azure Resource Manager et sécurité basée sur les rôles** : la géo-réplication active comprend un ensemble [d’API Azure Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx) pour la gestion, dont des [applets de commande PowerShell basées sur Azure Resource Manager](sql-database-geo-replication-powershell.md). Ces API nécessitent l’utilisation de groupes de ressources et la prise en charge de la sécurité basée sur les rôles (RBAC). Pour plus d’informations sur l’implémentation de rôles d’accès, voir le [contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

> [!NOTE]
> Parmi les nouvelles fonctionnalités de géo-réplication active, plusieurs sont uniquement prises en charge avec [l’API REST de SQL Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) et les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) basées sur [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). L’[API REST (classique)](https://msdn.microsoft.com/library/azure/dn505719.aspx) et les [applets de commande Azure SQL Database (classique)](https://msdn.microsoft.com/library/azure/dn546723.aspx) sont prises en charge à des fins de compatibilité descendante. L’utilisation des API basées sur Azure Resource Manager est donc recommandée. 
> 
> 

### <a name="transact-sql"></a>Transact-SQL
| Commande | Description |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) |Utilise l’argument ADD SECONDARY ON SERVER afin de créer une base de données secondaire pour une base de données existante puis lance la réplication des données |
| [ALTER DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) |Utilise l’argument FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS pour basculer d’une base de données secondaire à une base de données principale afin de lancer le basculement |
| [ALTER DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) |Utilise l’argument REMOVE SECONDARY ON SERVER pour mettre fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [sys.geo_replication_links (base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx) |Retourne des informations concernant tous les liens de réplication existants pour chaque base de données sur le serveur logique de base de données SQL Azure. |
| [sys.dm_geo_replication_link_status (base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx) |Obtient l’heure de la dernière réplication, le dernier décalage de la réplication et d’autres informations sur le lien de réplication pour une base de données SQL spécifique. |
| [sys.dm_operation_status (base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) |Affiche l’état de toutes les opérations de base de données, y compris l’état des liens de réplication. |
| [sp_wait_for_database_copy_sync (base de données SQL Azure)](https://msdn.microsoft.com/library/dn467644.aspx) |oblige l’application à attendre que toutes les transactions validées sont répliquées et acceptées par la base de données secondaire active. |
|  | |

### <a name="powershell"></a>PowerShell
| Applet de commande | Description |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) |Obtient une ou plusieurs bases de données. |
| [New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) |Crée une base de données secondaire pour une base de données existante puis lance la réplication des données. |
| [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx) |Bascule d’une base de données secondaire à une base de données principale afin de lancer le basculement. |
| [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) |Met fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) |Obtient les liens de géo-réplication entre une base de données SQL Azure et un groupe de ressources ou SQL Server. |
|  | |

### <a name="rest-api"></a>API REST
| API | Description |
| --- | --- |
| [.Créer ou mettre à jour la base de données (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Crée, met à jour ou restaure une base de données principale ou secondaire. |
| [Créer ou mettre à jour l’état de la base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Retourne l’état durant une opération de création. |
| [Définir la base de données secondaire comme principale (basculement planifié)](https://msdn.microsoft.com/library/azure/mt575007.aspx) |Promouvoir une base de données secondaire dans un partenariat de géo-réplication afin qu’elle devienne la nouvelle base de données primaire. |
| [Définir la base de données secondaire comme principale (basculement non planifié)](https://msdn.microsoft.com/library/azure/mt582027.aspx) |Pour forcer un basculement vers la base de données secondaire et définir la base de données secondaire comme base de données principale. |
| [Obtenir des liens de réplication](https://msdn.microsoft.com/library/azure/mt600929.aspx) |Obtient tous les liens de réplication pour une base de données SQL donnée dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. |
| [Obtenir un lien de réplication](https://msdn.microsoft.com/library/azure/mt600778.aspx) |Obtient un liens de réplication spécifique pour une base de données SQL particulière dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. |
|  | |

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md).
* Pour en savoir plus sur les exigences d’authentification pour de nouveaux serveurs et bases de données primaires, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).


