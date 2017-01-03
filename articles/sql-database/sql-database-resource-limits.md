---
title: "Limites de ressources d’Azure SQL Database | Microsoft Docs"
description: "Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: b5306d864593898246d0e7ba81ed3f93253fab74
ms.openlocfilehash: 81746a6f893eab4fe2b951fb0d52bd1645d2a56c


---
# <a name="azure-sql-database-resource-limits"></a>Limites de ressources de base de données SQL Azure
## <a name="overview"></a>Vue d’ensemble
Azure SQL Database gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : la **gouvernance des ressources** et l’**application de limites**. Cette rubrique décrit ces deux domaines principaux de la gestion des ressources.

## <a name="resource-governance"></a>Gouvernance des ressources
L’un des objectifs de conception des niveaux de service Basique, Standard et Premium est que la base de données SQL Azure se comporte comme si elle s’exécutait sur sa propre machine, complètement isolée des autres bases de données. La gouvernance des ressources émule ce comportement. Si l’utilisation des ressources agrégées atteint les ressources maximum disponibles de processeur, de mémoire, d’E/S du journal et d’E/S des données affectées à la base de données, la gouvernance des données mettra en file d’attente les requêtes en exécution et affectera des ressources aux requêtes en file d’attente à mesure qu’elles se libèrent.

Comme sur une machine dédiée, l’utilisation de toutes les ressources disponibles entraîne une exécution plus longue des requêtes en cours d’exécution, ce qui peut provoquer des délais d’expiration de commande sur le client. Les applications avec la logique de nouvelle tentative agressive et les applications qui exécutent des requêtes sur la base de données avec une fréquence élevée peuvent rencontrer des messages d’erreur lorsque vous tentez d’exécuter de nouvelles requêtes et que la limite de demandes simultanées a été atteinte.

### <a name="recommendations"></a>Recommandations :
Surveillez l’utilisation des ressources, ainsi que les temps de réponse moyens des requêtes lorsque vous approchez de l’utilisation maximale d’une base de données. Si vous rencontrez des latences de requête supérieures, généralement, trois options s’offrent à vous :

1. Réduire la quantité de requêtes entrantes dans la base de données afin d’éviter l’expiration et l’accumulation de demandes.
2. Affecter un niveau de performance supérieur à la base de données.
3. Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la section Analyse/Compréhension de requêtes de l’article Guide des performances de base de données SQL Azure.

## <a name="enforcement-of-limits"></a>Application de limites
Les ressources autres que le processeur, la mémoire, les E/S de journal et les E/S de données sont appliquées en refusant les nouvelles demandes lorsque les limites sont atteintes. Les clients reçoivent un [message d’erreur](sql-database-develop-error-messages.md) en fonction de la limite qui a été atteinte.

Par exemple, le nombre de connexions à une base de données SQL ainsi que le nombre de demandes simultanées qui peuvent être traitées est limité. La base de données SQL permet un nombre de connexions à la base de données supérieur au nombre de demandes simultanées afin de prendre en charge le groupement de connexions. Bien que la quantité de connexions disponibles puisse facilement être contrôlée par l’application, la quantité de demandes parallèles est souvent bien plus difficile à estimer et à contrôler. Il est possible de rencontrer des erreurs notamment pendant les charges maximales, lorsque l’application envoie trop de demandes ou que la base de données atteint ses limites de ressources et commence à empiler les threads de travail en raison de l’exécution de requêtes plus longues.

## <a name="service-tiers-and-performance-levels"></a>Niveaux de service et niveaux de performances
Il existe des niveaux de service et de performances à la fois pour les pools élastiques et pour la base de données autonome.

### <a name="standalone-databases"></a>Bases de données autonomes
Les limites d’une base de données autonome sont définies par ses niveaux de service et de performances. Le tableau suivant décrit les caractéristiques des bases de données De base, Standard et Premium de bases de données à différents niveaux de performances.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pools élastiques
[Pools élastiques](sql-database-elastic-pool.md) partagent des ressources entre les bases de données du pool. Le tableau suivant décrit les caractéristiques des pools de base de données élastique De base, Standard et Premium.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Pour consulter une définition étendue de chaque ressource répertoriée dans les tableaux précédents, reportez-vous aux descriptions de la rubrique [Capacités et limites des niveaux de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Pour obtenir une présentation des niveaux de service, consultez [Niveaux de service et de performance de Base de données SQL Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Autres limites de SQL Database
| Domaine | Limite | Description |
| --- | --- | --- |
| Bases de données utilisant l’exportation automatique par abonnement |10 |L’exportation automatique vous permet de créer une planification personnalisée pour sauvegarder vos bases de données SQL. La version préliminaire de cette fonctionnalité se terminera le 1er mars 2017.  |
| Base de données par serveur |Jusqu'à 5000 |Jusqu'à 5000 bases de données sont autorisées par serveur sur les serveurs V12. |
| DTU par serveur |45000 |45000 DTU sont disponibles par serveur sur les serveurs V12 pour la configuration des bases de données, des pools élastiques et des entrepôts de données. |

## <a name="resources"></a>Ressources
[Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)

[Niveaux de service et niveaux de performances de la base de données SQL Azure](sql-database-service-tiers.md)

[Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md)




<!--HONumber=Nov16_HO3-->


