<properties
	pageTitle="Pool de base de données élastique pour les bases de données SQL | Microsoft Azure"
	description="Découvrez comment vous pouvez maîtriser la croissance exponentielle dans les bases de données SQL avec des pools de bases de données élastiques, qui sont un moyen de partager des ressources disponibles entre plusieurs bases de données."
	keywords="base de données élastique,bases de données sql"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Maîtrise de la croissance exponentielle dans les bases de données SQL à l'aide de pools de bases de données élastiques pour partager des ressources

Si vous êtes un développeur SaaS chargé d’administrer des dizaines, des centaines, voire des milliers de bases de données SQL, un pool de bases de données élastiques simplifie la création, la maintenance et la gestion des performances dans ces bases de données avec un budget contrôlé. Vous pouvez [créer un pool de bases de données élastiques](sql-database-elastic-pool-portal.md) pour vos bases de données SQL en quelques minutes à l’aide du portail Microsoft Azure, de PowerShell ou de C#.

Dans le cadre d'applications SaaS courantes, chaque base de données a le plus souvent un client différent, chacun avec une consommation de ressources imprévisible et variable (processeur, E/S, mémoire résumée avec eDTU). Avec ces fluctuations de la demande pour chaque base de données, il peut être difficile de prédire et donc provisionner les ressources. Vous disposez de deux options : le surprovisionnement des ressources de base de données sur la base de pics d'utilisation (et le surpaiement). Ou le sous-provisionnement pour réduire les coûts, au détriment des performances et de la satisfaction des clients pendant les pics.

Microsoft a créé les pools de bases de données élastiques spécifiquement pour vous aider à résoudre ce problème.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]


Les pools de base de données élastique fournissent une solution pour les clients qui ont besoin de s’assurer que leurs bases de données obtiennent les ressources de performances dont ils ont besoin, lorsqu’ils en ont besoin, tout en fournissant un mécanisme d’allocation de ressources simple et un budget prévisible. Il est possible d’avoir l’échelle de performances à la demande de bases de données individuelles au sein d’un pool de bases de données élastiques, car chaque base de données au sein d’un pool utilise les eDTU à partir d’un ensemble partagé associé au pool. Ainsi, les bases de données sous une charge importante peuvent consommer davantage pour satisfaire la demande, tandis que les bases de données sous une charge légère en consomment moins, et les bases de données sans charge ne consomment aucun eDTU. En fournissant des ressources pour le pool plutôt que pour les bases de données uniques, vous simplifiez non seulement la gestion de plusieurs bases de données, mais vous avez également un budget prévisible pour une charge de travail imprévisible dans le cas contraire.

Si plusieurs eDTU sont nécessaires pour répondre aux besoins d’un pool (des bases de données supplémentaires sont ajoutées à un pool ou le démarrage de bases de données commence à utiliser plusieurs eDTU), des eDTUs supplémentaires peuvent être ajoutés à un pool existant sans temps d’arrêt de la base de données ou impact négatif sur les bases de données. De même, si les eDTU supplémentaires ne sont plus nécessaires, ils peuvent être supprimés à partir d’un pool existant à tout moment.

![Bases de données SQL partageant des eDTU dans un pool de base de données élastique.][1]

Les bases de données qui sont d’excellents candidats pour les pools de base de données élastiques ont généralement des périodes d’activité et d’autres périodes d’inactivité. Prenons l’exemple ci-dessus avec lequel vous pouvez afficher l’activité d’une base de données, 4 bases de données et enfin un pool de bases de données élastiques avec 20 bases de données. Ces bases de données dont l’activité varie au fil du temps sont d’excellents candidats pour les pools de base de données élastiques, car elles ne sont pas toutes actives en même temps et peuvent partager des eDTU. Toutes les bases de données n'adoptent pas ce schéma. Il existe des bases de données qui ont une demande en ressources constante. Elles sont mieux adaptées aux niveaux de service Basic, Standard et Premium dans lesquels les ressources sont attribuées individuellement. Pour obtenir de l’aide pour déterminer si vos bases de données tireraient parti d’un pool de base de données élastique, consultez [Considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md).

Pour en savoir plus sur les pools de bases de données élastiques, y compris les détails des API et des erreurs, consultez [Référence sur les pools de bases de données élastiques](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12.

## Gestion en toute facilité d’un grand nombre de bases de données SQL grâce aux outils de bases de données élastiques

En plus de rendre plus efficace l’utilisation des ressources et d’offrir une meilleure prévisibilité des performances, les pools de bases de données élastiques facilitent également le développement d’applications SaaS grâce à des outils qui simplifient la création et la gestion de votre couche de données. L'exécution de tâches de maintenance et de modifications dans un large ensemble de bases de données, qui était jusqu'ici un processus complexe et long, n'est plus aujourd'hui qu'une question d'exécution de scripts dans des tâches élastiques. La possibilité de créer et d’exécuter une tâche de base de données élastique élimine en grande partie la lourde charge de travail associée à l’administration de centaines, voire de milliers, de bases de données. Pour en savoir plus sur l’exécution du service de tâches de bases de données élastiques, qui permet l’exécution de scripts Transact-SQL dans toutes les bases de données élastiques d’un pool, consultez la page [Vue d’ensemble des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

Un large ensemble d'outils de développement puissants est également disponible pour la mise en œuvre de schémas d'applications de bases de données élastiques. Pour les bases de données partitionnées, d'autres outils tels que le fractionnement et la fusion vous offrent la possibilité de fractionner les données d'une seule partition pour les fusionner dans une autre. Cela réduit considérablement le travail de gestion de bases de données partitionnées à grande échelle. Pour en savoir plus, consultez les [Rubriques sur l'infrastructure élastique de base de données](sql-database-elastic-scale-documentation-map.md).

## Fonctionnalités de continuité des activités pour les bases de données d’un pool

Dans la version préliminaire actuelle, les bases de données élastiques prennent en charge la plupart des [fonctionnalités de continuité des activités](sql-database-business-continuity.md) disponibles sur les bases de données uniques sur des serveurs V12.

### Sauvegarde et restauration de bases de données (Limite de restauration dans le temps)

Les bases de données qui se trouvent dans un pool de bases de données élastiques sont sauvegardées automatiquement par le système et la stratégie de rétention de la sauvegarde est la même que le niveau de service correspondant pour les bases de données uniques. Plus précisément, une base de données élastique dans un pool Basic peut être restaurée à n’importe quel point de restauration dans les 7 derniers jours, une base de données élastique dans un pool Standard peut être restaurée à n’importe quel point de restauration au cours des 14 derniers jours et une base de données élastique dans un pool Premium peut être restaurée à n’importe quel point de restauration au cours des 35 derniers jours. Lors de l’aperçu, les bases de données dans un pool seront restaurées vers une base de données dans le même pool. Les bases de données supprimées seront toujours restaurées comme une base de données autonome en dehors du pool dans le niveau de performance le plus bas pour ce niveau de service. Par exemple, une base de données élastique dans un pool Standard est supprimée sera restaurée comme une base de données S0. Vous pouvez effectuer des opérations de restauration de bases de données à l'aide du portail Azure ou par programmation en utilisant l'API REST. La prise en charge des applets de commande PowerShell sera bientôt disponible.

### Restauration géographique

La restauration géographique vous permet de récupérer une base de données d'un pool dans un serveur situé dans une autre région. Pour restaurer une base de données dans un pool sur un autre serveur dans la version préliminaire, le serveur cible doit avoir un pool avec le même nom que le pool source. Le cas échéant, créez un pool sur le serveur cible et donnez-lui le même nom avant de restaurer la base de données. Si aucun pool portant le même nom n'existe sur le serveur cible, l'opération de restauration géographique échoue. Pour plus d’informations, consultez [Récupération à l’aide de la géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Utiliser la géo-réplication

La géo-réplication est disponible pour n’importe quelle base de données d’un pool de base de données élastique Standard ou Premium. Une ou plusieurs bases de données d’un partenariat de géo-réplication peuvent être situées dans un pool de base de données élastique tant que les niveaux de service sont identiques. Vous pouvez configurer la géo-réplication pour les pools de bases de données élastiques à l’aide du [portail Azure](sql-database-geo-replication-portal.md), de [PowerShell](sql-database-geo-replication-powershell.md) ou de [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### Importation et exportation

Par contre, il est possible d’exporter une base de données depuis un pool. Actuellement, l’importation d’une base de données directement dans un pool n’est pas prise en charge, mais vous pouvez importer dans une base de données unique puis déplacer la base de données dans un pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_1203_2015-->