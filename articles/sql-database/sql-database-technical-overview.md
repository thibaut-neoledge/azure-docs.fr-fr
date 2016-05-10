<properties
	pageTitle="Définition de la base de données SQL Présentation de la base de données SQL | Microsoft Azure"
	description="Introduction à la base de données SQL : détails techniques et fonctionnalités du système de gestion des bases de données relationnelles Microsoft dans le cloud."
	keywords="introduction à sql, intro à sql, qu’est-ce qu’une base de données sql"
	services="sql-database"
	documentationCenter=""
	authors="shontnew"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="03/29/2016"
   ms.author="shkurhek"/>

# Définition de la base de données SQL Présentation de SQL Database

La base de données SQL est un service de base de données relationnelle dans le cloud, qui repose sur le moteur Microsoft SQL Server numéro un du marché et propose des fonctionnalités stratégiques intégrées. SQL Database offre des performances prévisibles et une extensibilité sans interruption de service, et assure une continuité des activités et une protection des données, le tout sans presque aucune administration. Vous pouvez vous concentrer sur le développement rapide de vos applications et accélérer leur mise sur le marché, plutôt que de passer du temps à gérer des machines virtuelles et leur infrastructure. Parce qu’elle est basée sur le moteur [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), la base de données SQL prend en charge les outils, bibliothèques et API SQL Server existants, ce qui permet une migration et une extension vers le cloud plus faciles.

Cet article présente les principaux concepts et fonctionnalités de la base de données SQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Si vous êtes prêt à sauter le pas, vous pouvez [créer votre première base de données SQL](sql-database-get-started.md) ou [créer un pool de bases de données élastiques](sql-database-elastic-pool-create-portal.md) en quelques minutes. Si vous souhaitez poursuivre, regardez cette vidéo de 30 minutes.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## Ajustez les performances et la mise à l'échelle sans interruption de service

Les bases de données SQL sont disponibles en différents *niveaux de service* : de base, standard et premium. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour quelques euros par mois, puis [modifier le niveau de service](sql-database-scale-up.md) manuellement ou automatiquement à tout moment quand votre application devient connue à travers le monde entier, sans temps mort pour votre application ou vos clients.

Pour de nombreuses entreprises et applications, être en mesure de créer des bases de données et d'augmenter ou ralentir les performances d'une base de données unique à la demande est suffisant, surtout si les modèles d'utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial.

Les [pools de base de données élastique](sql-database-elastic-pool.md) dans SQL Database sont une solution à ce problème. Le concept est simple. Vous allouez des performances à un pool et financez les performances collectives du pool plutôt que les performances de la base de données unique. Vous n'avez pas besoin de ralentir ou d'augmenter les performances de la base de données. Les bases de données du pool, appelées *bases de données élastiques*, évoluent automatiquement à la hausse ou à la baisse pour répondre à la demande. Les bases de données élastiques consomment mais ne dépassent pas les limites du pool. Votre coût reste prévisible, même si l'utilisation de la base de données ne l'est pas. Qui plus est, vous pouvez [ajouter et supprimer les bases de données du pool](sql-database-elastic-pool-manage-portal.md), mettre à l’échelle votre application en passant de quelques bases de données à des milliers, le tout au sein d’un budget que vous contrôlez.

Dans les deux cas (base de données unique ou élastique), vous maîtrisez votre évolution. Vous pouvez fusionner des bases de données uniques avec des pools de bases de données élastiques et modifier les niveaux de service de bases de données uniques et de pools pour créer des conceptions innovantes. En outre, grâce à la puissance et à la portée d’Azure, vous pouvez mélanger les différents services Azure avec SQL Database, afin de pouvoir répondre aux besoins uniques de vos nouvelles applications en matière de conception, réduire les coûts, optimiser l’efficacité et créer de nouvelles opportunités professionnelles.

Mais comment comparer les performances relatives des bases de données et des pools de bases de données ? Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? La réponse se situe dans l’unité de transaction de base de données (DTU) pour les bases de données uniques et la DTU élastique (eDTU) pour les bases de données élastiques et les pools de bases de données. Voir [Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) pour plus d’informations.

## Votre application et votre activité ne s’arrêtent jamais

Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service [(SLA)](http://azure.microsoft.com/support/legal/sla/) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque base de données SQL, vous tirez parti de la protection des données intégrée, d'une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint de concevoir, acheter, créer et gérer. Même dans ce cas, en fonction des besoins de votre entreprise, vous pouvez exiger des couches supplémentaires de protection afin que votre application et votre entreprise puissent revenir à la normale rapidement en cas de sinistre, d'une erreur ou autre événement. Avec SQL Database, chaque niveau de service offre un ensemble spécifique de fonctionnalités que vous pouvez utiliser pour être opérationnel et le rester. Vous pouvez utiliser la limite de restauration dans le temps pour renvoyer une base de données à un état antérieur, jusqu'à 35 jours. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez basculer vers des réplicas de base de données dans une autre région. Sinon, vous pouvez utiliser des réplicas pour des mises à niveau ou un déplacement dans différentes régions.

![Géo-réplication de base de données SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Consultez [Continuité de l’activité](sql-database-business-continuity.md) pour plus d’informations sur les différentes fonctionnalités de continuité d’activité disponibles pour les différents niveaux de service.

## Sécurisez vos données
SQL Server est largement reconnu pour son expertise en matière de sécurité des données que la base de données SQL confirme avec des fonctionnalités qui limitent l'accès, protègent les données et vous aident à surveiller l'activité. Pour un récapitulatif rapide des options de sécurité dont vous disposez dans la base de données SQL, voir [Sécurisation de votre base de données SQL](sql-database-security.md) Pour obtenir une vue plus complète des fonctionnalités de sécurité, voir [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL](https://msdn.microsoft.com/library/bb510589). Enfin, rendez-vous dans le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/security/) pour plus d’informations sur la sécurité de la plateforme Azure.

## Étapes suivantes
Maintenant que vous avez lu l’introduction à la base de données SQL et répondu à la question « Qu’est ce qu’une base de données SQL ? », vous êtes prêt à effectuer les opérations suivantes :

- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/) pour obtenir un comparatif des coûts entre une base de données unique et une base de données élastique et accéder à des calculateurs.
- En savoir plus sur les [pools élastiques](sql-database-elastic-pool.md).
- Commencez par [créer votre première base de données](sql-database-get-started.md).
- [Se connecter et lancer des requêtes avec SSMS](sql-database-connect-query-ssms.md)
- Créez votre première application en C#, Java, Node.js, PHP, Python ou Ruby : [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)
- Voir l’index des titres et descriptions de l’article [All topics for Azure sql-database service](sql-database-index-all-articles.md) (Ensemble des rubriques relatives au service de base de données SQL Azure).

<!---HONumber=AcomDC_0504_2016-->