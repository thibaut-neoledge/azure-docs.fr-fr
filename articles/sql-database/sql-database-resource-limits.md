---
title: "Limites de ressources d’Azure SQL Database | Microsoft Docs"
description: "Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure."
services: sql-database
documentationcenter: na
author: janeng
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: f2e48e290f59efb5ab6271b7b2882ca8ea8887a6
ms.openlocfilehash: 15fd64f21eb20ae412f4d0cf1930e99e2e9fc62b


---
# <a name="azure-sql-database-resource-limits"></a>Limites de ressources de base de données SQL Azure
## <a name="overview"></a>Vue d’ensemble
Azure SQL Database gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : la **gouvernance des ressources** et l’**application de limites**. Cette rubrique décrit ces deux domaines principaux de la gestion des ressources.

## <a name="resource-governance"></a>Gouvernance des ressources
L’un des objectifs de conception des niveaux de service De base, Standard et Premium est qu’Azure SQL Database se comporte comme si la base de données s’exécutait sur sa propre machine, de façon isolée des autres bases de données. La gouvernance des ressources émule ce comportement. Si l’utilisation des ressources agrégées atteint les ressources maximales disponibles de processeur, de mémoire, d’E/S du journal et d’E/S des données affectées à la base de données, la gouvernance des données met en file d’attente les requêtes en exécution et affecte des ressources aux requêtes en file d’attente à mesure qu’elles se libèrent.

Comme sur une machine dédiée, l’utilisation de toutes les ressources disponibles entraîne une exécution plus longue des requêtes en cours d’exécution, ce qui peut provoquer des expirations de commandes sur le client. Les applications avec la logique de nouvelle tentative agressive et les applications qui exécutent des requêtes sur la base de données avec une fréquence élevée peuvent rencontrer des messages d’erreur lorsque vous tentez d’exécuter de nouvelles requêtes et que la limite de demandes simultanées a été atteinte.

### <a name="recommendations"></a>Recommandations :
Surveillez l’utilisation des ressources et les temps de réponse moyens des requêtes lorsque vous approchez de l’utilisation maximale d’une base de données. Si vous rencontrez des latences de requête supérieures, généralement, trois options s’offrent à vous :

1. Réduire le nombre de requêtes entrantes dans la base de données afin d’éviter l’expiration et l’accumulation de demandes.
2. Affecter un niveau de performance supérieur à la base de données.
3. Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la section Analyse/Compréhension de requêtes de l’article Guide des performances de base de données SQL Azure.

## <a name="enforcement-of-limits"></a>Application de limites
Les ressources autres que le processeur, la mémoire, les E/S de journal et les E/S de données sont appliquées en refusant les nouvelles demandes lorsque les limites sont atteintes. Lorsqu’une base de données atteint la limite de taille maximale configurée, les insertions et les mises à jour qui augmentent la taille des données échouent, tandis que les sélections et les suppressions continuent de fonctionner. Les clients reçoivent un [message d’erreur](sql-database-develop-error-messages.md) en fonction de la limite qui a été atteinte.

Par exemple, le nombre de connexions à une base de données SQL et le nombre de demandes simultanées qui peuvent être traitées sont limités. La base de données SQL permet un nombre de connexions à la base de données supérieur au nombre de demandes simultanées afin de prendre en charge le groupement de connexions. Si le nombre de connexions disponibles peut être facilement contrôlé par l’application, le nombre de demandes parallèles est souvent plus difficile à estimer et à contrôler. Il est possible de rencontrer des erreurs notamment pendant les charges maximales, lorsque l’application envoie trop de demandes ou que la base de données atteint ses limites de ressources et commence à empiler les threads de travail en raison de l’exécution de requêtes plus longues.

## <a name="service-tiers-and-performance-levels"></a>Niveaux de service et niveaux de performances
Il existe des niveaux de service et de performances à la fois pour les pools élastiques et pour les bases de données uniques.

### <a name="single-databases"></a>Bases de données uniques
Pour une base de données unique, les limites d’une base de données sont définies par ses niveaux de service et de performances. Le tableau suivant décrit les caractéristiques des bases de données De base, Standard et Premium de bases de données à différents niveaux de performances.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pools élastiques
[Pools élastiques](sql-database-elastic-pool.md) partagent des ressources entre les bases de données du pool. Le tableau ci-après décrit les caractéristiques des pools élastiques De base, Standard et Premium.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Pour consulter une définition étendue de chaque ressource répertoriée dans les tableaux précédents, reportez-vous aux descriptions de la rubrique [Capacités et limites des niveaux de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Pour obtenir une présentation des niveaux de service, consultez [Niveaux de service et de performance de Base de données SQL Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Autres limites de SQL Database
| Domaine | Limite | Description |
| --- | --- | --- |
| Bases de données utilisant l’exportation automatique par abonnement |10 |L’exportation automatique vous permet de créer une planification personnalisée pour sauvegarder vos bases de données SQL. La version préliminaire de cette fonctionnalité se terminera le 1er mars 2017.  |
| Base de données par serveur |Jusqu'à 5000 |Jusqu'à 5000 bases de données sont autorisées par serveur sur les serveurs V12. |
| DTU par serveur |45000 |45&000; DTU sont disponibles par serveur sur des serveurs V12 pour l’approvisionnement des bases de données, des pools élastiques et des entrepôts de données. |

> [!IMPORTANT]
> La fonctionnalité Automatiser l’exportation Azure SQL Database est maintenant disponible en version préliminaire et sera supprimée le 1er mars 2017. À partir du 1er décembre 2016, vous ne pourrez plus configurer l’exportation automatisée sur une base de données SQL. Tous vos travaux d’exportation automatisée existants continueront à fonctionner jusqu’au 1er mars 2017. Après le 1er décembre 2016, vous pouvez utiliser [Rétention des sauvegardes à long terme](sql-database-long-term-retention.md) ou [Azure Automation](../automation/automation-intro.md) pour archiver des bases de données SQL régulièrement à l’aide de PowerShell en fonction d’une planification de votre choix. Pour obtenir un exemple de script, vous pouvez télécharger [l’exemple de script sur GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>


## <a name="resources"></a>les ressources
[Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)

[Niveaux de service et niveaux de performances de la base de données SQL Azure](sql-database-service-tiers.md)

[Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md)




<!--HONumber=Feb17_HO2-->


