<properties 
	pageTitle="Maîtrise de la croissance avec les bases de données élastiques" 
	description="Un pool de bases de données élastiques Base de données SQL Azure est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/25/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Maîtrise de la croissance avec les bases de données élastiques

Si vous êtes un développeur SaaS chargé d’administrer des dizaines, des centaines, voire des milliers de bases de données, un pool de bases de données élastiques simplifie la création, la maintenance et la gestion des performances dans ces bases de données avec un budget contrôlé.

Dans le cadre d'applications SaaS courantes, chaque base de données a le plus souvent un client différent, chacun avec une consommation de ressources imprévisible et variable (processeur, E/S, mémoire résumée avec eDTU). Avec ces fluctuations de la demande pour chaque base de données, il peut être difficile de prédire et donc provisionner les ressources. Vous disposez de deux options : le surprovisionnement des ressources de base de données sur la base de pics d'utilisation (et le surpaiement). Ou le sous-provisionnement pour réduire les coûts, au détriment des performances et de la satisfaction des clients pendant les pics.

Microsoft a créé les pools de bases de données élastiques spécifiquement pour vous aider à résoudre ce problème.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par les bases de données élastiques dans le pool. Vous pouvez ajouter des bases de données au pool ou les en supprimer à tout moment. Les bases de données du pool partagent les ressources (exprimées en unités de débit de base de données élastique ou eDTU) et la capacité de stockage du pool, mais chaque base de données utilise uniquement les ressources dont elle a besoin quand elle en a besoin, libérant ainsi les ressources pour d’autres bases de données si nécessaire. Au lieu de sur-approvisionner des bases de données individuelles et de payer pour des ressources inutilisées, vous allouez globalement à un coût prévisible les ressources du pool. Ceci permet de répartir le coût pour adopter un modèle commercial compétitif et d’adapter les performances de chaque base de données.

Les bases de données susceptibles d'être intégrées à des pools de bases de données élastiques ne sont généralement actives que moins 50 % du temps. Généralement, les bases de données sont parfois inactives, actives avec de faibles demandes en ressources ou actives avec des demandes en ressources élevées. Toutes les bases de données n'adoptent pas ce schéma. Il existe des bases de données qui ont une demande en ressources constante. Elles sont mieux adaptées aux niveaux de service Basic, Standard et Premium dans lesquels les ressources sont attribuées individuellement. Pour obtenir de l’aide pour déterminer si vos bases de données tireraient parti d’un pool de base de données élastique, consultez [Considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md).

Vous pouvez créer un pool de bases de données élastiques en quelques minutes à l’aide du portail Microsoft Azure ou de PowerShell. Pour plus d'informations, consultez [Créer et gérer un pool élastique](sql-database-elastic-pool-portal.md). Pour en savoir plus sur les pools de bases de données élastiques, y compris les détails des API et des erreurs, consultez [Référence des bases de données élastiques](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Les pools élastiques sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12.

## Gestion en toute facilité d’un grand nombre de bases de données grâce aux outils de bases de données élastiques

En plus de rendre plus efficace l’utilisation des ressources et d’offrir une meilleure prévisibilité des performances, les pools de bases de données élastiques facilitent également le développement d’applications SaaS grâce à des outils qui simplifient la création et la gestion de votre couche de données. L'exécution de tâches de maintenance et de modifications dans un large ensemble de bases de données, qui était jusqu'ici un processus complexe et long, n'est plus aujourd'hui qu'une question d'exécution de scripts dans des tâches élastiques. La possibilité de créer et d’exécuter une tâche de base de données élastique élimine en grande partie la lourde charge de travail associée à l’administration de centaines, voire de milliers, de bases de données. Pour en savoir plus sur l’exécution du service de tâches de bases de données élastiques, qui permet l’exécution de scripts T-SQL dans toutes les bases de données élastiques d’un pool, consultez la page [Vue d’ensemble des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

Un large ensemble d'outils de développement puissants est également disponible pour la mise en œuvre de schémas d'applications de bases de données élastiques. Pour les bases de données partitionnées, d'autres outils tels que le fractionnement et la fusion vous offrent la possibilité de fractionner les données d'une seule partition pour les fusionner dans une autre. Cela réduit considérablement le travail de gestion de bases de données partitionnées à grande échelle. Pour en savoir plus, consultez les [Rubriques sur l'infrastructure élastique de base de données](sql-database-elastic-scale-documentation-map.md).

## Fonctionnalités de continuité des activités pour les bases de données d’un pool

Dans la version préliminaire actuelle, les bases de données d’un pool de bases de données élastiques (dans le niveau élastique standard) prennent en charge la plupart des fonctionnalités disponibles pour les bases de données de niveau Standard.

### Sauvegarde et restauration de bases de données (Limite de restauration dans le temps)

Les bases de données qui se trouvent dans un pool de bases de données élastiques sont sauvegardées automatiquement par le système et la stratégie de rétention de la sauvegarde est la même que pour les bases de données de niveau Standard. Dans la version préliminaire, les bases de données du pool sont restaurées dans une nouvelle base de données dans le même pool. Les bases de données supprimées sont toujours restaurées comme bases de données autonomes en dehors du pool en tant que bases de données Standard S0. Vous pouvez effectuer des opérations de restauration de bases de données à l'aide du portail Azure ou par programmation en utilisant l'API REST. La prise en charge des applets de commande PowerShell sera bientôt disponible.

### Restauration géographique

La restauration géographique vous permet de récupérer une base de données d'un pool dans un serveur situé dans une autre région. Pour restaurer une base de données dans un pool sur un autre serveur dans la version préliminaire, le serveur cible doit avoir un pool avec le même nom que le pool source. Le cas échéant, créez un pool sur le serveur cible et donnez-lui le même nom avant de restaurer la base de données. Si aucun pool portant le même nom n'existe sur le serveur cible, l'opération de restauration géographique échoue. Vous pouvez effectuer des opérations de restauration géographique à l'aide du portail Azure ou de l'API REST. La prise en charge des applets de commande PowerShell sera bientôt disponible.


### Utiliser la géo-réplication

Les bases de données pour lesquelles la géo-réplication est déjà activée peuvent être déplacées vers et depuis un pool de bases de données élastiques, et la réplication fonctionne normalement. Actuellement, dans la version préliminaire, vous ne pouvez pas activer la géo-réplication sur une base de données qui se trouve déjà dans un pool.



 

<!---HONumber=July15_HO5-->