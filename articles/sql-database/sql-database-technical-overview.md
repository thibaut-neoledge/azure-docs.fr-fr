---
title: "Définition de la base de données SQL Présentation de SQL Database | Microsoft Docs"
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
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 37534ff6366bd519cec50bc033b2bcd8f8bda5c7


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Définition de la base de données SQL Présentation de SQL Database
La base de données SQL est un service de base de données relationnelle dans le cloud, qui repose sur le moteur Microsoft SQL Server numéro un du marché et propose des fonctionnalités stratégiques intégrées. SQL Database offre des performances prévisibles et une extensibilité sans interruption de service, et assure une continuité des activités et une protection des données, le tout sans presque aucune administration. Vous pouvez vous concentrer sur le développement rapide de vos applications et accélérer leur mise sur le marché, plutôt que de passer du temps à gérer des machines virtuelles et leur infrastructure. Parce qu’elle est basée sur le moteur [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , la base de données SQL prend en charge les outils, bibliothèques et API SQL Server existants, ce qui permet une migration et une extension vers le cloud plus faciles.

Cet article présente les principaux concepts et fonctionnalités de la base de données SQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Si vous êtes prêt, vous pouvez [créer votre première base de données SQL](sql-database-get-started.md) ou [créer un pool élastique](sql-database-elastic-pool-create-portal.md) en quelques minutes. Si vous souhaitez poursuivre, regardez cette vidéo de 30 minutes.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustez les performances et la mise à l'échelle sans interruption de service
Les bases de données SQL sont disponibles en différents *niveaux de service*: de base, standard et premium. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour quelques euros par mois, puis [modifier le niveau de service](sql-database-scale-up.md) manuellement ou automatiquement à tout moment quand votre application devient connue à travers le monde entier, sans temps mort pour votre application ou vos clients.

Pour de nombreuses entreprises et applications, la possibilité de créer des bases de données autonomes et d’augmenter ou ralentir les performances à la demande se révèle suffisante, surtout si les modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial.

Les [pools élastiques](sql-database-elastic-pool.md) dans SQL Database permettent de résoudre ce problème. Le concept est simple. Vous allouez des performances à un pool et financez les performances collectives du pool plutôt que les performances des bases de données autonomes. Vous n'avez pas besoin de ralentir ou d'augmenter les performances de la base de données. Les bases de données du pool, appelées *bases de données élastiques*, montent ou descendent automatiquement en puissance pour répondre à la demande. Les bases de données élastiques consomment mais ne dépassent pas les limites du pool. Votre coût reste prévisible, même si l'utilisation de la base de données ne l'est pas. Par ailleurs, vous pouvez [ajouter et supprimer des bases de données du pool](sql-database-elastic-pool-manage-portal.md), mettre à l’échelle votre application en passant de quelques bases de données à des milliers, le tout dans les limites d’un budget que vous contrôlez. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).

Dans les deux cas (base de données unique ou élastique), vous maîtrisez votre évolution. Vous pouvez fusionner des bases de données autonomes avec des pools élastiques et modifier les niveaux de service de bases de données autonomes et de pools rapidement et en toute simplicité, pour adapter le système à votre situation. En outre, grâce à la puissance et à la portée d’Azure, vous pouvez mélanger les autres services Azure avec SQL Database, afin de pouvoir répondre aux besoins uniques de vos applications en matière de conception, réduire les coûts, optimiser l’efficacité et créer de nouvelles opportunités professionnelles.

Mais comment comparer les performances relatives des bases de données et des pools de bases de données ? Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? La réponse se trouve dans les outils intégrés de surveillance des performances et d’alerte, combinés aux évaluations de performances basées sur les unités de transaction de base de données (DTU) pour les bases de données autonomes et sur les DTU élastiques (eDTU) pour les bases de données élastiques et les pools de bases de données. Vous pouvez ainsi évaluer rapidement l’impact d’une montée ou d’une descente en puissance en fonction de vos besoins actuels ou des besoins de votre projet en matière de performances. Voir [Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) pour plus d’informations.

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service [(SLA)](http://azure.microsoft.com/support/legal/sla/)d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque base de données SQL, vous tirez parti de la sécurité intégrée, d'une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Même dans ce cas, en fonction des exigences de votre entreprise, vous pouvez avoir besoin de couches supplémentaires de protection afin que votre application et votre entreprise puissent revenir à la normale rapidement en cas de sinistre, d'une erreur ou de toute autre interruption. Avec SQL Database, chaque niveau de service offre un ensemble complet de fonctionnalités et d’options assurant la continuité des activités, que vous pouvez utiliser pour démarrer votre base de données et vous assurer qu’elle fonctionne de manière continue. Vous pouvez utiliser la limite de restauration dans le temps pour renvoyer une base de données à un état antérieur, jusqu'à 35 jours. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez restaurer les bases de données à partir de copies géo-redondantes des sauvegardes récentes ou basculer vers des réplicas de base de données dans une autre région. Vous pouvez également utiliser des réplicas pour des mises à niveau ou un déplacement dans d’autres régions.

![Géo-réplication de base de données SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Consultez [Continuité de l’activité](sql-database-business-continuity.md) pour plus d’informations sur les différentes fonctionnalités de continuité d’activité disponibles pour les différents niveaux de service.

## <a name="secure-your-data"></a>Sécurisez vos données
SQL Server est largement reconnu pour son expertise en matière de sécurité des données que SQL Database confirme avec des fonctionnalités qui limitent l’accès, protègent les données et vous aident à surveiller l’activité. Pour un récapitulatif rapide des options de sécurité dont vous disposez dans SQL Database, consultez [Sécurisation de votre base de données SQL Database](sql-database-security.md). Pour obtenir une vue plus complète des fonctionnalités de sécurité, consultez [Centre de sécurité pour le moteur de base de données SQL Server et SQL Database](https://msdn.microsoft.com/library/bb510589). Enfin, visitez le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/security/) pour plus d’informations sur la sécurité de la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu l’introduction à la base de données SQL et répondu à la question « Qu’est ce qu’une base de données SQL ? », vous êtes prêt à effectuer les opérations suivantes :

* Pour consulter un comparatif des coûts des bases de données autonomes et des pools élastiques et pour accéder à des calculateurs, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/).
* En savoir plus sur les [pools élastiques](sql-database-elastic-pool.md).
* Commencez par [créer votre première base de données](sql-database-get-started.md).
* [Se connecter et lancer des requêtes avec SSMS](sql-database-connect-query-ssms.md)
* Créez votre première application en C#, Java, Node.js, PHP, Python ou Ruby : [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)



<!--HONumber=Dec16_HO2-->


