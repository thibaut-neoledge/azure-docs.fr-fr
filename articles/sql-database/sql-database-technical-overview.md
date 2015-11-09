<properties
   pageTitle="Présentation de SQL Database | Microsoft Azure"
   description="Découvrez les fonctionnalités et détails techniques d’Azure SQL Database, système de gestion de base de données relationnelle (SGBDR) de Microsoft et solution PaaS dans le cloud."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# Présentation de SQL Database

SQL Database est un service de base de données relationnelle dans le cloud, qui repose sur le moteur Microsoft SQL Server numéro un du marché et propose des fonctionnalités stratégiques intégrées. SQL Database offre des performances prévisibles et une extensibilité sans interruption de service, et assure une continuité des activités et une protection des données, le tout sans presque aucune administration. Vous pouvez vous concentrer sur le développement rapide de vos applications et accélérer leur mise sur le marché, plutôt que de passer du temps à gérer des machines virtuelles et leur infrastructure. Parce qu’il est basé sur le moteur [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), le service SQL Database prend en charge les outils, bibliothèques et API SQL Server existants, ce qui permet une migration et une extension vers le cloud plus faciles.

Cet article présente les principaux concepts et fonctionnalités de SQL Database qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Si vous êtes prêt à sauter le pas, vous pouvez [créer votre première base de données SQL](sql-database-get-started.md) ou [créer un pool de bases de données élastiques](sql-database-elastic-pool-portal.md) en quelques minutes. Si vous souhaitez poursuivre, regardez cette vidéo de 30 minutes.


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## Ajustez les performances et la mise à l'échelle sans interruption de service
Les bases de données SQL sont disponibles en plusieurs *niveaux de service* : De base, Standard et Premium. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour traiter des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour quelques euros par mois, puis [modifier le niveau de service](sql-database-scale-up.md) manuellement ou par programmation à tout moment quand votre application devient connue à travers le monde entier, sans interruption de service pour votre application ou vos clients.

Pour de nombreuses entreprises et applications, être en mesure de créer des bases de données et d'augmenter ou ralentir les performances d'une base de données unique à la demande est suffisant, surtout si les modèles d'utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial.

Les [pools de bases de données élastiques](sql-database-elastic-pool.md) dans SQL Database sont une solution à ce problème. Le concept est simple. Vous allouez des performances à un pool et financez les performances collectives du pool plutôt que les performances de la base de données unique. Vous n'avez pas besoin de ralentir ou d'augmenter les performances de la base de données. Les bases de données du pool, appelées *bases de données élastiques*, évoluent automatiquement à la hausse ou à la baisse pour répondre à la demande. Les bases de données élastiques consomment mais ne dépassent pas les limites du pool. Votre coût reste prévisible, même si l'utilisation de la base de données ne l'est pas. Qui plus est, vous pouvez [ajouter ou supprimer des bases de données dans le pool](sql-database-elastic-pool-portal.md), mettre à l’échelle votre application en passant de quelques bases de données à des milliers, le tout dans la limite d’un budget que vous contrôlez.

Dans les deux cas (base de données unique ou élastique), vous maîtrisez votre évolution. Vous pouvez fusionner des bases de données uniques avec des pools de bases de données élastiques et modifier les niveaux de service de bases de données uniques et de pools pour créer des conceptions innovantes. En outre, grâce à la puissance et à la portée d’Azure, vous pouvez mélanger les différents services Azure avec SQL Database, afin de pouvoir répondre aux besoins uniques de vos nouvelles applications en matière de conception, réduire les coûts, optimiser l’efficacité et créer de nouvelles opportunités professionnelles.

Mais comment comparer les performances relatives des bases de données et des pools de bases de données ? Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? La réponse se situe dans l'unité de transaction de base de données (DTU) pour les bases de données uniques et la DTU élastique (eDTU) pour les bases de données élastiques et les pools de bases de données.

## Comprendre les DTU

[AZURE.INCLUDE [Description de la DTU d'une base de données SQL](../../includes/sql-database-understanding-dtus.md)]

## Votre application et votre activité ne s’arrêtent jamais

Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service [(SLA)](http://azure.microsoft.com/support/legal/sla/) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque base de données SQL, vous tirez parti de la protection des données intégrée, d'une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint de concevoir, acheter, créer et gérer. Même dans ce cas, en fonction des besoins de votre entreprise, vous pouvez exiger des couches supplémentaires de protection afin que votre application et votre entreprise puissent revenir à la normale rapidement en cas de sinistre, d'une erreur ou autre événement. Avec SQL Database, chaque niveau de service offre un ensemble spécifique de fonctionnalités que vous pouvez utiliser pour être opérationnel. Vous pouvez utiliser la limite de restauration dans le temps pour renvoyer une base de données à un état antérieur, jusqu'à 35 jours. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez basculer vers des réplicas de base de données dans une autre région. Sinon, vous pouvez utiliser des réplicas pour des mises à niveau ou un déplacement dans différentes régions.

![Géo-réplication de base de données SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Consultez [Continuité de l’activité](sql-database-business-continuity.md) pour plus d’informations sur les différentes fonctionnalités de continuité d’activité disponibles pour les différents niveaux de service.

## Sécurisez vos données
SQL Server est largement reconnu pour son expertise en matière de sécurité des données que la base de données SQL confirme avec des fonctionnalités qui limitent l'accès, protègent les données et vous aident à surveiller l'activité. Consultez [Sécurisation de votre base de données SQL](sql-database-security.md) pour un récapitulatif rapide des options de sécurité dont vous disposez dans Base de données SQL. Consultez [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL](https://msdn.microsoft.com/library/bb510589) pour obtenir une vue plus complète des fonctionnalités de sécurité. Enfin, rendez-vous dans le [Centre de gestion de la confidentialité Azure](http://azure.microsoft.com/support/trust-center/security/) pour plus d’informations sur la sécurité de la plateforme Azure.

## Étapes suivantes

- Consultez la [page de tarification](http://azure.microsoft.com/pricing/details/sql-database/) pour une base de données unique et les tarifs et calculatrices pour les bases de données élastiques.

- Commencez par [créer votre première base de données](sql-database-get-started.md). Puis, créez votre première application en [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) ou [Ruby](sql-database-develop-ruby-simple-linux).

<!---HONumber=Nov15_HO1-->