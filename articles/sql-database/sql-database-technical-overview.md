---
title: "Qu’est-ce que le service Azure SQL Database ? | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introduction à sql, intro à sql, qu’est-ce qu’une base de données sql"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 36a9fd78f5c53891f38bc3a8356cef666f4d0ca2
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Définition de la base de données SQL Présentation de SQL Database
SQL Database est un service de base de données relationnelle dans le cloud Microsoft, qui repose sur le moteur Microsoft SQL Server numéro un du marché, pouvant traiter des charges de travail stratégiques. SQL Database offre des performances prévisibles à plusieurs niveaux de service, une extensibilité sans interruption de service, une continuité des activités intégrée et une protection des données, le tout sans presque aucune administration. Ces fonctionnalités vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à la gestion des machines virtuelles et de leur infrastructure. Comme SQL Database est basé sur le moteur [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), SQL Database prend en charge les outils, les bibliothèques et les API SQL Server existants. Il vous est ainsi plus facile de développer de nouvelles solutions, de déplacer vos solutions SQL Server existantes et de les étendre au cloud Microsoft sans avoir besoin d’une formation particulière.

Cet article présente les principaux concepts et fonctionnalités de la base de données SQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Consultez ces démarrages rapides pour bien commencer :
 - [Créer une base de données SQL dans le portail Azure](sql-database-get-started-portal.md)  
 - [Créer une base de données SQL avec Azure CLI](sql-database-get-started-cli.md)
 - [Créer une base de données SQL à l’aide de PowerShell](sql-database-get-started-powershell.md)

Pour accéder à des exemples Azure CLI et PowerShell, consultez :
 - [Exemples Azure CLI pour Azure SQL Database](sql-database-cli-samples.md)
 - [Exemples Azure PowerShell pour Azure SQL Database](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustez les performances et la mise à l'échelle sans interruption de service
SQL Database propose trois niveaux de service : De base, Standard et Premium, ainsi que Premium RS. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour quelques euros par mois et [modifier le niveau de service](sql-database-service-tiers.md) manuellement ou automatiquement à tout moment pour répondre aux besoins de votre solution. Cette opération ne perturbe pas le fonctionnement de votre application, ni l’expérience de vos clients. L’évolutivité dynamique permet à votre base de données de répondre en toute transparence aux besoins en ressources qui évoluent sans cesse et de payer uniquement les ressources dont vous avez besoin, lorsque vous en avez besoin.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools élastiques pour optimiser l’utilisation des ressources
Pour de nombreuses entreprises et applications, la possibilité de créer des bases de données uniques et d’augmenter ou de ralentir les performances à la demande se révèle suffisante, surtout si les modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial. [Les pools élastiques](sql-database-elastic-pool.md) sont conçus pour résoudre ce problème. Le concept est simple. Vous allouez des ressources de performance à un pool plutôt qu’à une base de données individuelle et vous payez pour les ressources de performance d’un pool plutôt que pour celles d’une base de données unique. Les pools élastiques vous permettent de ne pas avoir à ajuster les performances de la base de données en fonction des besoins en ressources. Les bases de données regroupées consomment les ressources de performance du pool élastique en fonction des besoins. Les bases de données regroupées consomment mais ne dépassent pas les limites du pool. Vos coûts restent prévisibles, même si l’utilisation de la base de données individuelle ne l’est pas. Par ailleurs, vous pouvez [ajouter et supprimer des bases de données du pool](sql-database-elastic-pool-manage-portal.md), mettre à l’échelle votre application en passant de quelques bases de données à des milliers, le tout dans les limites d’un budget que vous contrôlez. Enfin, vous pouvez également contrôler le nombre minimum et maximum de ressources disponibles pour les bases de données dans le pool. Cela vous permet de garantir qu’aucune base de données du pool n’utilise toutes les ressources du pool et que chaque base de données regroupée dispose d’un nombre minimum de ressources. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).

## <a name="blend-single-databases-with-pooled-databases"></a>Fusionner les bases de données uniques avec les bases de données regroupées
Quelle que soit la méthode utilisée (bases de données uniques ou pools élastiques), rien n’est irréversible. Vous pouvez fusionner des bases de données uniques avec des pools élastiques et modifier les niveaux de service des bases de données uniques et des pools élastiques rapidement et en toute simplicité, pour adapter le système à votre situation. En outre, grâce à la puissance et à la portée d’Azure, vous pouvez mélanger les autres services Azure avec SQL Database, afin de pouvoir répondre aux besoins uniques de vos applications en matière de conception, réduire les coûts, optimiser l’efficacité et créer de nouvelles opportunités professionnelles.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Mais comment comparer les performances relatives des bases de données uniques et des pools élastiques ? Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? Vous utilisez les outils [d’alerte](sql-database-insights-alerts-portal.md) et [de surveillance des performances intégrés](sql-database-performance.md) en plus des évaluations de performances basées sur [les unités de transaction de base de données (DTU) pour les bases de données uniques et les DTU élastiques (eDTU) pour les pools élastiques](sql-database-what-is-a-dtu.md). Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. Voir [Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) pour plus d’informations.

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service [(SLA)](http://azure.microsoft.com/support/legal/sla/)d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque base de données SQL, vous tirez parti de la sécurité intégrée, d’une tolérance en cas de panne et de la [protection des données](sql-database-automated-backups.md) que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Avec SQL Database, chaque niveau de service offre un ensemble complet de fonctionnalités et d’options assurant la continuité des activités, que vous pouvez utiliser pour démarrer votre base de données et vous assurer qu’elle fonctionne de manière continue. Vous pouvez utiliser la [limite de restauration dans le temps](sql-database-recovery-using-backups.md) pour renvoyer une base de données à un état antérieur, jusqu’à 35 jours. Vous pouvez configurer la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md) pour stocker les sauvegardes dans un coffre sécurisé pendant 10 ans maximum. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez restaurer les bases de données à partir de [copies géo-redondantes des sauvegardes récentes](sql-database-recovery-using-backups.md). Si nécessaire, vous pouvez également configurer des [réplicas lisibles géo-redondants](sql-database-geo-replication-overview.md) dans une ou plusieurs régions pour un basculement rapide en cas de panne du centre de données. Vous pouvez également utiliser ces réplicas pour améliorer les performances en lecture dans différentes régions géographiques ou pour les [mises à niveau de l’application sans temps d’arrêt](sql-database-manage-application-rolling-upgrade.md). 

![Géoréplication de SQL Database](./media/sql-database-technical-overview/azure_sqldb_map.png)

Consultez [Continuité de l’activité](sql-database-business-continuity.md) pour plus d’informations sur les différentes fonctionnalités de continuité d’activité disponibles pour les différents niveaux de service.

## <a name="secure-your-data"></a>Sécurisez vos données
SQL Server est reconnu pour son expertise en matière de sécurité des données que SQL Database confirme avec des fonctionnalités qui limitent l’accès, protègent les données et vous aident à surveiller l’activité. Pour un récapitulatif rapide des options de sécurité dont vous disposez dans SQL Database, consultez [Sécurisation de votre base de données SQL Database](sql-database-security-overview.md). Pour obtenir une vue plus complète des fonctionnalités de sécurité, consultez [Centre de sécurité pour le moteur de base de données SQL Server et SQL Database](https://msdn.microsoft.com/library/bb510589). Enfin, visitez le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/security/) pour plus d’informations sur la sécurité de la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu l’introduction à la base de données SQL et répondu à la question « Qu’est ce qu’une base de données SQL ? », vous êtes prêt à effectuer les opérations suivantes :

* Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/) pour obtenir un comparatif des coûts entre une base de données unique et des pools élastiques et accéder à des calculateurs.
* En savoir plus sur les [pools élastiques](sql-database-elastic-pool.md).
* Commencez par [créer votre première base de données](sql-database-get-started-portal.md).
* Créez votre première application en C#, Java, Node.js, PHP, Python ou Ruby : [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)

