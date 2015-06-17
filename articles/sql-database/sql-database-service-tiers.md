<properties
   pageTitle="Niveaux du service relatifs aux bases de données SQL"
   description="Comparez les performances et les fonctionnalités de continuité d’activité des différents niveaux de service des bases de données SQL Microsoft Azure, afin de trouver le juste équilibre entre les coûts et les capacités en matière d’évolutivité à la demande sans coupure de service."
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
   ms.date="04/15/2015"
   ms.author="shkurhek"/>

# Niveaux de service

Les niveaux de service associés à la [base de données SQL](sql-database-technical-overview.md) sont le niveau de base, le niveau Standard et le niveau Premium. Ces trois niveaux proposent un [contrat SLA](http://azure.microsoft.com/support/legal/sla/) garantissant un temps d’activité de 99,99 %. Par ailleurs, ils proposent des performances prévisibles, des options de continuité d’activité d’entreprise professionnelles, des fonctionnalités de gestion de la sécurité et une facturation à l’heure. Comme vous pouvez accéder à plusieurs niveaux de performances au sein de chaque niveau de service, vous avez la possibilité de choisir celui qui répond le mieux aux besoins de votre charge de travail. Si vous avez besoin de faire monter le système en puissance ou de réduire son échelle, vous pouvez très simplement modifier ses niveaux dans le portail Microsoft Azure, sans occasionner de coupure de service pour votre application. Pour en savoir plus, voir [Modification des niveaux de service et de performances de base de données](https://msdn.microsoft.com/library/azure/dn369872.aspx).

> [AZURE.IMPORTANT]Les éditions « Web et Business » seront retirées. Découvrez comment [mettre à niveau les éditions « Web et Business »](sql-database-upgrade-new-service-tiers.md). Si vous souhaitez continuer à utiliser ces éditions, reportez-vous à la [FAQ Sunset](http://azure.microsoft.com/pricing/details/sql-database/web-business/).

## De base

Le niveau de base est conçu pour les applications présentant une charge de travail transactionnelle légère. Cas d’utilisation classique : une application légère qui requiert une petite base de données, avec une seule opération, à un moment donné.

**Performances, taille et fonctionnalités**


| Niveau de service | De base |
| :-- | :-- |
| Unités de débit de la base de données (DTU) | 5 |
| Taille maximale de la base de données | 2 Go |
| Limite de restauration dans le temps | Jusqu’à la milliseconde, au cours des 7 derniers jours |
| Récupération d’urgence | Géo-restauration, restauration dans toute région de Microsoft Azure |
| Objectifs en termes de performances | Taux de transactions par heure |


## Standard

Le niveau Standard est l’option incontournable pour toute prise en main des charges de travail transactionnelles. Il offre de meilleures performances, ainsi que des fonctionnalités intégrées de continuité d’activité plus efficaces que le niveau de base. Cas d’utilisation classique : une application proposant plusieurs transactions simultanées.

**Performances et taille**


| Niveau de service | Standard S0 | Standard S1 | Standard S2 | Standard S3 |
| :-- | :-- | :-- | :-- | :-- |
| Unités de débit de la base de données (DTU) | 10 | 20 | 50 | 100 |
| Taille maximale de la base de données | 250 Go | 250 Go | 250 Go | 250 Go |


**Découverte**


| Niveau de service | Standard (S0, S1, S2 et S3) |
| :-- | :-- |
| Limite de restauration dans le temps | Jusqu’à la milliseconde, au cours des 14 derniers jours |
| Récupération d’urgence | Géo-réplication standard, 1 système secondaire hors ligne |
| Objectifs en termes de performances | Taux de transactions par minute |


## Premium

Le niveau Premium est conçu pour les applications stratégiques. Il offre le meilleur niveau de performances et propose un accès à des fonctionnalités avancées de continuité d’activité, notamment la géo-réplication active dans un maximum de 4 régions Microsoft Azure de votre choix. Cas d’utilisation classique : une application stratégique incluant un important volume de transactions et de nombreux utilisateurs simultanés.

**Performances et taille**


| Niveau de service | Premium P1 | Premium P2 | Premium P3 |
| :-- | :-- | :-- | :-- |
| Unités de débit de la base de données (DTU) | 125 | 250 | 1 000 |
| Taille maximale de la base de données | 500 Go | 500 Go | 500 Go |


**Découverte**


| Niveau de service | Premium (P1, P2 et P3) |
| :-- | :-- |
| Limite de restauration dans le temps | Jusqu’à la milliseconde, au cours des 35 derniers jours |
| Récupération d’urgence | Géo-réplication active, jusqu’à 4 systèmes secondaires accessibles en lecture en ligne |
| Objectifs en termes de performances | Taux de transactions par seconde |


Pour en savoir plus sur la tarification de ces niveaux, voir [Base de données SQL - Tarification](http://azure.microsoft.com/pricing/details/sql-database/).

Maintenant que vous êtes au fait des différents niveaux de base de données SQL, essayez-les, grâce à notre version [d’essai gratuit](http://azure.microsoft.com/pricing/free-trial/), et découvrez [comment créer votre première base de données SQL](sql-database-get-started.md) !

<!---HONumber=58--> 