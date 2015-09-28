<properties
   pageTitle="Présentation de SQL Database"
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
   ms.date="07/17/2015"
   ms.author="shkurhek"/>

# Présentation de la base de données SQL

La base de données SQL est un service de base de données relationnelle dans le cloud, qui repose sur le moteur Microsoft SQL Server numéro un du marché et propose des fonctionnalités stratégiques intégrées. La base de données SQL offre des fonctions prévisibles de gestion des performances, de l'évolutivité sans temps d'interruption, de la continuité d'activité et de la protection des données, ainsi qu'une administration proche de zéro. Vous pouvez vous concentrer sur le développement rapide de vos applications et accélérer leur mise sur le marché, plutôt que de passer du temps à gérer des ordinateurs virtuels et leur infrastructure. Parce qu'il est basé sur le moteur [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), le service Base de données SQL prend en charge les outils, bibliothèques et API SQL Server existants, ce qui vous facilite un éventuel développement dans le cloud.

Cet article présente les principaux concepts et fonctionnalités de la base de données SQL liés aux performances, à l'évolutivité et à la facilité de gestion de ce produit, et s'accompagne de liens pour en explorer tous les détails. Si vous êtes prêt à sauter le pas, vous pouvez [créer votre première base de données SQL](sql-database-get-started.md) ou [créer un pool de base de données élastique](sql-database-elastic-pool-portal.md) en quelques minutes.

## Ajustez les performances et la mise à l'échelle sans interruption de service
Les bases de données SQL sont disponibles en différents *niveaux de service* : de base, standard et premium. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour quelques euros par mois, puis [modifier le niveau de service](sql-database-scale-up.md) manuellement ou automatiquement à tout moment quand votre application devient connue à travers le monde entier, sans temps mort pour votre application ou vos clients.

Pour de nombreuses entreprises et applications, être en mesure de créer des bases de données et d'augmenter ou ralentir les performances d'une base de données unique à la demande est suffisant, surtout si les modèles d'utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial.

Les [pools de base de données élastiques](sql-database-elastic-pool.md) de la base de données SQL sont une solution à ce problème. Le concept est simple. Vous allouez des performances à un pool et financez les performances collectives du pool plutôt que les performances de la base de données unique. Vous n'avez pas besoin de ralentir ou d'augmenter les performances de la base de données. Les bases de données du pool, appelées *bases de données élastiques*, évoluent automatiquement à la hausse ou à la baisse pour répondre à la demande. Les bases de données élastiques consomment mais ne dépassent pas les limites du pool. Votre coût reste prévisible, même si l'utilisation de la base de données ne l'est pas. Qui plus est, vous pouvez [ajouter et supprimer les bases de données du pool](sql-database-elastic-pool-portal.md), mettre à l'échelle votre application en passant de quelques bases de données à des milliers, le tout au sein d'un budget que vous contrôlez.

Dans les deux cas (base de données unique ou élastique), vous maîtrisez votre évolution. Vous pouvez fusionner des bases de données uniques avec des pools de base de données élastiques et modifier les niveaux de service de bases de données uniques et de pools pour créer des conceptions innovantes. En outre, grâce à la puissance et à la portée de Microsoft Azure, vous pouvez mélanger les différents services Microsoft Azure avec Base de données SQL, afin de pouvoir répondre aux besoins professionnels uniques de vos nouvelles applications, réduire les coûts, optimiser l'efficacité et mettre en lumière de nouvelles opportunités professionnelles.

Mais comment comparer les performances relatives des bases de données et des pools de base de données ? Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? La réponse se situe dans l'unité de transaction de base de données (DTU) pour les bases de données uniques et la DTU élastique (eDTU) pour les bases de données élastiques et les pools de base de données.

## Comprendre les DTU

L'unité de transaction de base de données (DTU) est l'unité de mesure dans la base de données SQL qui représente la puissance relative des bases de données selon une mesure réelle : la transaction de base de données. Nous avons étudié un ensemble d'opérations qui sont classiques pour une demande de traitement transactionnel en ligne, puis mesuré le nombre de transactions pouvant être effectuées par seconde dans des conditions de charge complète (ceci est la version courte, vous pouvez lire les détails dans [Vue d'ensemble des tests d'évaluation](https://msdn.microsoft.com/library/azure/dn741327.aspx)).

Une base de données de base comprend 5 DTU, ce qui signifie qu'elle peut effectuer 5 transactions par seconde, alors qu'une base de données Premium P11 comprend 1 750 DTU.

![DTU de base de données unique](./media/sql-database-technical-overview/single_db_dtus.png)

La DTU pour les bases de données uniques se traduit directement par l'eDTU pour les bases de données élastiques. Par exemple, une base de données dans un pool de bases de données élastiques de base présente jusqu'à 5 eDTU. Ce sont les mêmes performances qu'avec une base de données de base unique. La différence est que la base de données élastique n'utilise pas d'eDTU du pool tant qu'elle n'y est pas obligée.

![Pools élastiques et eDTU](./media/sql-database-technical-overview/sqldb_elastic_pools.png)

Voici un exemple simple pour mieux comprendre. Prenons un pool de bases de données élastiques de base avec 1 000 DTU et déposons 800 bases de données dans ce pool. Tant qu'il n'y a pas plus de 200 bases de données sur les 800 qui sont utilisées à n'importe quel moment (5 DTU X 200 = 1 000), vous n'atteignez pas le seuil de capacité du pool et les performances de la base de données ne sont pas affectées. Cet exemple est simplifié à des fins de clarté. Le calcul mathématique réel est un peu plus complexe. Le portail effectue le calcul pour vous et émet une recommandation basée sur l'utilisation historique de la base de données. Consultez [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md) pour comprendre le fonctionnement des recommandations ou effectuer les calculs vous-même.

## Votre application et votre activité ne s'essoufflent jamais

Le contrat de niveau de service avec disponibilité à 99,99 % hors pair d'Azure [(SLA)](http://azure.microsoft.com/support/legal/sla/), appuyé par un réseau mondial de centres de données gérés par Microsoft, permet de maintenir votre application en cours d'exécution 24/7. Avec chaque base de données SQL, vous tirez parti de la protection des données intégrée, d'une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint de concevoir, acheter, créer et gérer. Même dans ce cas, en fonction des besoins de votre entreprise, vous pouvez exiger des couches supplémentaires de protection afin que votre application et votre entreprise puissent revenir à la normale rapidement en cas de sinistre, d'une erreur ou autre événement du genre. Avec la base de données SQL, chaque niveau de service offre un ensemble spécifique de fonctionnalités que vous pouvez utiliser pour être opérationnel. Vous pouvez utiliser la limite de restauration dans le temps pour renvoyer une base de données à un état antérieur, jusqu'à 35 jours. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez basculer vers des répliquas de base de données dans une autre région. Sinon, vous pouvez utiliser des répliquas pour des mises à niveau ou un déplacement dans différentes régions.

![Géo-réplication de base de données SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Voir [Continuité de l'activité](sql-database-business-continuity.md) pour plus d'informations sur les fonctionnalités de continuité des activités métier différentes disponibles pour les différents niveaux de service.

## Sécurisez vos données
SQL Server est largement reconnu pour son expertise en matière de sécurité des données que la base de données SQL confirme avec des fonctionnalités qui limitent l'accès, protègent les données et vous aident à surveiller l'activité. Voir [Sécurisation de votre base de données SQL](sql-database-security.md) pour un récapitulatif rapide des options de sécurité dont vous disposez dans la base de données SQL. Voir [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL](https://msdn.microsoft.com/library/bb510589) pour une vue plus complète des fonctionnalités de sécurité. Enfin, rendez-vous dans le [Centre de gestion de la confidentialité Azure](http://azure.microsoft.com/support/trust-center/security/) pour plus d'informations sur la sécurité des plates-formes d'Azure.

## Étapes suivantes

- Consultez la [page de tarification](http://azure.microsoft.com/pricing/details/sql-database/) pour une base de données unique et les tarifs et calculatrices pour les bases de données élastiques.

- Commencez par [créer votre première base de données](sql-database-get-started.md). Puis créez votre première application [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) ou [Ruby](sql-database-develop-ruby-simple-linux).

<!---HONumber=Sept15_HO3-->