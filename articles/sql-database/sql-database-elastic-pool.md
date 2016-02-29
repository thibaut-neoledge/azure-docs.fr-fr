<properties
	pageTitle="Pool de base de données élastique pour les bases de données SQL | Microsoft Azure"
	description="Découvrez comment vous pouvez maîtriser la croissance exponentielle dans les bases de données SQL avec des pools de bases de données élastiques, qui sont un moyen de partager des ressources disponibles entre plusieurs bases de données."
	keywords="base de données élastique,bases de données sql"	
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/11/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Maîtrise de la croissance exponentielle dans les bases de données SQL à l'aide de pools de bases de données élastiques pour partager des ressources

Un développeur SaaS doit créer et gérer des dizaines, des centaines, voire des milliers de bases de données SQL. Les pools élastiques simplifient la création, la maintenance et la gestion des performances des bases de données dans le cadre d’un budget que vous contrôlez. Ajoutez des bases de données au pool ou retirez-en à volonté. [Créez un pool de base de données élastique](sql-database-elastic-pool-portal.md) pour vos bases de données SQL en quelques minutes à l’aide du portail Microsoft Azure, de [PowerShell](sql-database-elastic-pool-powershell.md) ou de [C#](sql-database-elastic-pool-csharp.md).

Pour plus d’informations sur les API et les erreurs, consultez [Informations de référence sur les pools de base de données élastique](sql-database-elastic-pool-reference.md).

## Fonctionnement

Un modèle d’application SaaS courant consiste à attribuer une base de données à chaque client. Les besoins en ressources de chaque client (base de données) sont imprévisibles (processeur, E/S, mémoire exprimés en DTU). Avec ces pics et creux de demande, comment allouer des ressources ? Deux options : (1) le surprovisionnement des ressources sur la base de pics d’utilisation (et le surpaiement). Ou (2) le sous-provisionnement pour réduire les coûts, au détriment des performances et de la satisfaction des clients pendant les pics.

Les pools de base de données élastique sont une solution à ce problème.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Grâce à ces derniers, les bases de données obtiennent les ressources de performances dont elles ont besoin, quand elles en ont besoin, dans le cadre d’un mécanisme d’allocation de ressources simple et d’un budget prévisible. Un pool bénéficie d’un nombre défini d’eDTU, pour un prix donné. Au sein du pool, les différentes bases de données peuvent en toute souplesse s’adapter automatiquement en fonction des paramètres définis. Si la charge est élevée, une base de données peut consommer plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges légères en consomment moins, tandis que celles qui ne sont soumises à aucune charge n’en consomment pas du tout. L’approvisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données uniques simplifie vos tâches de gestion. En outre, vous disposez d’un budget prévisible pour le pool.

Vous pouvez ajouter des eDTU à un pool existant sans que les bases de données connaissent de temps d’arrêt ou soient affectées. De même, si les eDTU supplémentaires ne sont plus nécessaires, ils peuvent être supprimés à partir d’un pool existant à tout moment.

De plus, vous pouvez ajouter des bases de données au pool ou en retirer. Si une base de données finit par sous-utiliser les ressources, retirez-la.

## Quelles bases de données mettre dans un pool ?

![Bases de données SQL partageant des eDTU dans un pool de base de données élastique.][1]

Les bases de données qui sont d’excellents candidats pour les pools de base de données élastiques ont généralement des périodes d’activité et d’autres périodes d’inactivité. Dans l’exemple ci-dessus, vous affichez l’activité d’une base de données unique, de 4 bases de données et enfin d’un pool de base de données élastique avec 20 bases de données. Les bases de données dont l’activité varie au fil du temps sont d’excellents candidats pour les pools élastiques, car elles ne sont pas toutes actives en même temps et peuvent partager des eDTU. Toutes les bases de données n'adoptent pas ce schéma. Les bases de données qui ont une demande en ressources constante sont mieux adaptées aux niveaux de service De base, Standard et Premium dans lesquels les ressources sont affectées individuellement.

[Considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs SQL Database V12.

## Tâches de base de données élastiques

Un pool simplifie les tâches de gestion grâce à l’exécution des scripts dans des **[tâches élastiques](sql-database-elastic-jobs-overview.md)**. Une tâche de base de données élastique élimine pratiquement le caractère fastidieux d’un nombre élevé de bases de données. Pour commencer, consultez [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md).

Pour plus d’informations sur les autres outils de bases de données élastiques, consultez [Parcours d’apprentissage des outils de base de données élastique](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## Fonctionnalités de continuité des activités pour les bases de données d’un pool

Dans la version préliminaire actuelle, les bases de données élastiques prennent en charge la plupart des [fonctionnalités de continuité des activités](sql-database-business-continuity.md) disponibles sur les bases de données uniques sur des serveurs V12.

### Limite de restauration dans le temps

Les bases de données qui se trouvent dans un pool de bases de données élastiques sont sauvegardées automatiquement par le système et la stratégie de rétention de la sauvegarde est la même que le niveau de service correspondant pour les bases de données uniques. Pour résumer, les bases de données dans chaque niveau ont une plage de restauration différente :

* **Pool de base** : restauration possible à un point quelconque au cours des 7 derniers jours. 
* **Pool standard** : restauration possible à un point quelconque au cours des 14 derniers jours.
* **Pool premium** : restauration possible à un point quelconque au cours des 35 derniers jours. 

Lors de l’aperçu, les bases de données dans un pool seront restaurées vers une base de données dans le même pool. Les bases de données supprimées seront toujours restaurées comme une base de données autonome en dehors du pool dans le niveau de performance le plus bas pour ce niveau de service. Par exemple, une base de données élastique dans un pool Standard est supprimée sera restaurée comme une base de données S0. Vous pouvez effectuer des opérations de restauration de bases de données à l'aide du portail Azure ou par programmation en utilisant l'API REST. La prise en charge des applets de commande PowerShell sera bientôt disponible.

### Restauration géographique

La restauration géographique vous permet de récupérer une base de données d'un pool dans un serveur situé dans une autre région. Pour restaurer une base de données dans un pool sur un autre serveur dans la version préliminaire, le serveur cible doit avoir un pool avec le même nom que le pool source. Le cas échéant, créez un pool sur le serveur cible et donnez-lui le même nom avant de restaurer la base de données. Si aucun pool portant le même nom n'existe sur le serveur cible, l'opération de restauration géographique échoue. Pour plus d’informations, consultez [Récupération à l’aide de la géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Utiliser la géo-réplication

La géo-réplication est disponible pour n’importe quelle base de données d’un pool de base de données élastique Standard ou Premium. Une ou plusieurs bases de données d’un partenariat de géo-réplication peuvent être situées dans un pool de base de données élastique tant que les niveaux de service sont identiques. Vous pouvez configurer la géo-réplication pour les pools de bases de données élastiques à l’aide du [portail Azure](sql-database-geo-replication-portal.md), de [PowerShell](sql-database-geo-replication-powershell.md) ou de [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### Importation et exportation

Par contre, il est possible d’exporter une base de données depuis un pool. Actuellement, l’importation d’une base de données directement dans un pool n’est pas prise en charge, mais vous pouvez importer dans une base de données unique puis déplacer la base de données dans un pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0218_2016-->