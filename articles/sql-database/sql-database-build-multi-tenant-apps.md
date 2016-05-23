<properties
   pageTitle="Créer des applications mutualisées avec le service Base de données SQL Azure"
   description="Découvrez comment créer des applications mutualisées avec le service Base de données SQL Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/04/2016"
   ms.author="carlrab"/>

# Créer des applications mutualisées avec le service Base de données SQL Azure

## Tirer parti des pools élastiques pour créer des applications mutualisées plus efficaces

En tant que développeur SaaS, si vous écrivez une application mutualisée avec de nombreux clients à gérer, vous devez souvent faire des compromis entre les performances, la gestion et la sécurité. Avec les pools de bases de données élastiques du service Base de données SQL Azure, ce n’est plus le cas. Ces pools vous permettent de gérer et de surveiller les applications mutualisées, tout en bénéficiant d’avantages d’avantages en termes d’isolation, avec un client par base de données.

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## Une mise à l’échelle automatique dont vous avez le contrôle

Les pools mettent à l’échelle les performances et la capacité de stockage des bases de données automatiquement, à la volée. Vous pouvez contrôler les performances affectées à un pool, ajouter ou supprimer des bases de données élastiques à la demande et définir les performances des bases de données sans la moindre incidence sur le coût global du pool. Vous n’avez donc pas à vous soucier de la gestion de l’utilisation des bases de données individuelles.

[Lire la documentation](sql-database-elastic-pool.md)

## Gestion intelligente de votre environnement

Les recommandations de dimensionnement intégrées identifient de manière proactive les bases de données qui pourraient tirer parti des pools. Ces recommandations permettent une analyse par simulation qui assure une optimisation rapide, contribuant à la réalisation de vos objectifs de performances. Des tableaux de bord complets de surveillance et de dépannage des performances vous aident à visualiser l’utilisation historique des pools.

[Lire la documentation](sql-database-elastic-pool-guidance.md)

## Des performances et des prix adaptés à vos besoins

Les pools De base, Standard et Premium vous offrent un large éventail de performances, de capacités de stockage et d’options de tarification. Les pools peuvent contenir jusqu’à 400 bases de données élastiques. Les bases de données élastiques permettent la mise à l’échelle automatique de pas moins de 1 000 unités de transaction de base de données élastique (eDTU).

[Lire la documentation](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## Outils élastiques

En plus des pools élastiques, le service Base de données SQL Azure intègre des fonctionnalités qui vous aident à gérer les activités opérationnelles sur plusieurs bases de données :

**Exécutez des requêtes et des rapports sur plusieurs bases de données.** Les [requêtes de base de données élastique](sql-database-elastic-query-overview.md) vous permettent d’exécuter des requêtes ou des rapports sur les bases de données de votre pool et d’accéder simultanément aux données distantes stockées dans de nombreuses bases de données de votre pool.

**Exécutez des transactions dans plusieurs bases de données.** Les [transactions de base de données élastique](sql-database-elastic-transactions-overview.md) vous permettent d’exécuter des transactions portant sur plusieurs bases de données SQL et d’exécuter des opérations (par exemple, lors du traitement de transactions financières dans les bases de données ou de la mise à jour de l’inventaire d’une base de données et des commandes).

**Exécutez les mêmes opérations sur plusieurs bases de données.** Les [tâches de base de données élastique](sql-database-elastic-jobs-overview.md) vous permettent d’exécuter des opérations administratives telles que la reconstruction d’index ou la mise à jour des schémas dans chaque base de données de votre pool élastique.

Accédez à la page d’accueil pour voir les autres avantages du service Base de données SQL. [Voyez par vous-même](https://azure.microsoft.com/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->