<properties
	pageTitle="Qu’est-ce qu’un pool de base de données élastique ? | Microsoft Azure"
	description="Gérez des centaines voire des milliers de bases de données à l’aide d’un pool. Un seul prix pour un ensemble d’unités de performances peut être distribué dans le pool. Déplacez les bases de données à votre convenance."
	keywords="base de données élastique,bases de données sql"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/12/2016"
	ms.author="CarlRabeler"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Qu’est-ce qu’un pool de base de données élastique ?

Les pools élastiques offrent une solution simple et économique pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables et non prévisibles.

> [AZURE.NOTE] Les pools élastiques sont mis à la disposition générale dans toutes les régions Azure, à l’exception du Nord du centre des États-Unis, de l’Inde de l’Ouest et du Nord de la Chine, où ils sont actuellement en préversion. Les pools élastiques seront mis à la disposition générale dans ces régions dès que possible.

## Fonctionnement

Un modèle d’application SaaS courant est un modèle de base de données avec un seul client : chaque client se voit attribuer une base de données. Les besoins en ressources de chaque client (base de données) en matière de mémoire, d’E/S et de processeur sont imprévisibles. Avec ces pics et creux de demande, comment allouer des ressources ? En règle générale, vous avez deux options : (1) le sur-approvisionnement de ressources en fonction des pics d’utilisation et des coûts trop élevés ou (2) le sous-approvisionnement pour réduire les coûts, au détriment des performances et de la satisfaction client pendant les pics. Grâce aux pools de base de données élastiques, les bases de données obtiennent les ressources de performances dont elles ont besoin, quand elles en ont besoin, dans le cadre d’un mécanisme d’allocation de ressources simple et d’un budget prévisible. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Dans la base de données SQL, la mesure relative d’une capacité de base de données pour traiter des demandes de ressources est exprimée en unités de transaction de base de données (DTU)pour les bases de données uniques et en DTU élastiques (eDTU) pour les pools de base de données élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez la page [Présentation de la base de données SQL](sql-database-technical-overview.md#understand-dtus).

Un pool bénéficie d’un nombre défini d’eDTU, pour un prix donné. Au sein du pool, les différentes bases de données peuvent en toute souplesse s’adapter automatiquement en fonction des paramètres définis. Si la charge est élevée, une base de données peut consommer plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges légères en consomment moins, tandis que celles qui ne sont soumises à aucune charge n’en consomment pas du tout. L’approvisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données uniques simplifie vos tâches de gestion. En outre, vous disposez d’un budget prévisible pour le pool.

Vous pouvez ajouter des eDTU à un pool existant sans que les bases de données connaissent de temps d’arrêt ou soient affectées. De même, si les eDTU supplémentaires ne sont plus nécessaires, ils peuvent être supprimés à partir d’un pool existant à tout moment.

De plus, vous pouvez ajouter des bases de données au pool ou en retirer. Si une base de données finit par sous-utiliser les ressources, retirez-la.

## Quelles bases de données mettre dans un pool ?

![Bases de données SQL partageant des eDTU dans un pool de base de données élastique.][1]

Les bases de données qui sont d’excellents candidats pour les pools de base de données élastiques ont généralement des périodes d’activité et d’autres périodes d’inactivité. Dans l’exemple ci-dessus, vous affichez l’activité d’une base de données unique, de 4 bases de données et enfin d’un pool de base de données élastique avec 20 bases de données. Les bases de données dont l’activité varie au fil du temps sont d’excellents candidats pour les pools élastiques, car elles ne sont pas toutes actives en même temps et peuvent partager des eDTU. Toutes les bases de données n'adoptent pas ce schéma. Les bases de données qui ont une demande en ressources constante sont mieux adaptées aux niveaux de service De base, Standard et Premium dans lesquels les ressources sont affectées individuellement.

[Considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs SQL Database V12.

## Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques

[AZURE.INCLUDE [Tableau des niveaux de service de base de données SQL pour les bases de données élastiques](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Si toutes les DTU d’un pool élastique sont utilisées, chaque base de données du pool reçoit une quantité égale de ressources pour traiter les requêtes. Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. L’application de ce partage équitable des ressources s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de DTU par base de données est défini sur une valeur différente de zéro.

## Propriétés du pool de bases de données élastiques

### Limites relatives aux pools élastiques

| Propriété | Description |
| :-- | :-- |
| Niveau de service | De base, Standard ou Premium. Le niveau de service détermine les limites de stockage et de performances minimales et maximales qui peuvent être configurées, ainsi que les choix en matière de continuité d’activité. Chaque base de données au sein d’un pool a le même niveau de service que le pool. Le terme « édition » est synonyme de « niveau de service ». |
| Nombre d’eDTU par pool | Nombre maximal d’eDTU pouvant être partagées par les bases de données du pool. Le nombre total d’eDTU utilisées par les bases de données du pool ne peut pas dépasser la limite de ce pool à un moment donné. |
| Espace de stockage maximal par pool (Go) | Quantité maximale de stockage (en Go) pouvant être partagée par les bases de données du pool. Le stockage total utilisé par les bases de données du pool ne peut pas dépasser cette limite. Cette limite est déterminée par le nombre d’eDTU par pool. Si cette limite est dépassée, toutes les bases de données passent en lecture seule. |
| Nombre maximal de bases de données par pool | Nombre maximal de bases de données autorisées par pool. |
| Nombre maximal d’ouvriers simultanés par pool | Nombre maximal d’ouvriers simultanés (demandes) disponibles pour toutes les bases de données du pool. |
| Nombre maximal de connexions simultanées par pool | Nombre maximal de connexions simultanées pour toutes les bases de données du pool. |
| Nombre maximal de sessions simultanées par pool | Nombre maximal de sessions simultanées pour toutes les bases de données du pool. |


### Limites relatives aux bases de données élastiques

| Propriété | Description |
| :-- | :-- |
| Nombre maximal d’eDTU par base de données | Nombre maximal d’eDTU pouvant être utilisées par une des bases de données du pool en fonction du nombre eDTU utilisées par les autres bases de données du pool. Le nombre maximal d’eDTU par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de donnée. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal d’eDTU par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation. Par exemple, supposons que le pic d’utilisation par base de données est de 20 eDTU et que seules 20 % des 100 bases de données du pool connaissent simultanément un pic d’utilisation. Si le nombre maximal d’eDTU par base de données est défini sur 20 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le nombre d’eDTU par pool sur 400. |
| Nombre minimal d’eDTU par base de données | Nombre minimal d’eDTU garanti pour chaque base de données du pool. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal d’eDTU par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est généralement définie sur une valeur comprise entre 0 et le nombre moyen d’eDTU utilisées par base de données. Remarque : le produit du nombre de bases de données du pool et du nombre minimal d’eDTU par base de données ne peut pas dépasser le nombre d’eDTU par pool. Par exemple, si un pool comporte 20 bases de données et que le nombre minimal d’eDTU par base de données est défini sur 10 eDTU, le nombre d’eDTU par pool doit être d’au moins 200 eDTU. |
| Espace de stockage maximal par base de données (Go) | Espace de stockage maximal pour une base de données du pool. Les bases de données élastiques se partagent l’espace de stockage du pool. Par conséquent, le stockage de base de données est limité au stockage de pool minimal restant, dans la limite du stockage maximal par base de données.|


## Tâches de base de données élastiques

Un pool simplifie les tâches de gestion grâce à l’exécution des scripts dans des **[tâches élastiques](sql-database-elastic-jobs-overview.md)**. Une tâche de base de données élastique élimine pratiquement le caractère fastidieux d’un nombre élevé de bases de données. Pour commencer, consultez l’article [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md).

Pour plus d’informations sur les autres outils, consultez le [parcours d’apprentissage des outils de base de données élastique](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## Fonctionnalités de continuité des activités pour les bases de données d’un pool

Les bases de données élastiques prennent généralement en charge les mêmes [fonctionnalités de continuité des activités](sql-database-business-continuity.md) que celles disponibles pour les bases de données uniques sur des serveurs V12.


### Restauration dans le temps

La restauration dans le temps utilise les sauvegardes automatiques de base de données pour récupérer une base de données d’un pool à un moment précis dans le temps. Voir [Récupération d’une base de données SQL Microsoft Azure suite à une erreur de l’utilisateur](sql-database-user-error-recovery.md)

### Restauration géographique

La restauration géographique constitue l’option de récupération par défaut lorsque la base de données est indisponible en raison d’un incident dans la région où la base de données est hébergée. Voir [Récupérer une base de données SQL Azure en cas de défaillance](sql-database-disaster-recovery.md)

### Géo-réplication active

Pour les applications qui ont des exigences de récupération extrêmement strictes auxquelles la restauration géographique ne peut pas répondre, configurez la géo-réplication active à l’aide du [portail Azure](sql-database-geo-replication-portal.md), de [PowerShell](sql-database-geo-replication-powershell.md) ou de [Transact-SQL](sql-database-geo-replication-transact-sql.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0720_2016-->