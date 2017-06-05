---
title: "Vue d’ensemble du service de base de données relationnelle d’Azure Database pour PostgreSQL | Microsoft Docs"
description: "Fournit une vue d’ensemble du service de base de données relationnelle d’Azure Database pour PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: overview
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8aae9994f8fc6bb8e4e5368a1495dd257be0417f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="what-is-azure-database-for-postgresql"></a>Qu’est-ce qu’Azure Database pour PostgreSQL ?

Azure Database pour PostgreSQL est un service de base de données relationnelle dans le cloud de Microsoft conçu pour les développeurs basés sur la version de la communauté du moteur de base de données [PostgreSQL](https://www.postgresql.org/) open source. Ce service est en version préliminaire publique. Azure Database pour PostgreSQL offre :
- Performances prévisibles sur plusieurs niveaux de service
- Évolutivité dynamique sans interruption de l’application
- Haute disponibilité intégrée
- Protection des données

Toutes ces fonctionnalités ne nécessitent presque aucune administration, et toutes sont fournies sans coût supplémentaire. Ces fonctionnalités vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à la gestion des machines virtuelles et de leur infrastructure. En outre, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix et fournir avec la vitesse et l’efficacité que vos activités professionnelles exigent sans avoir à acquérir de nouvelles compétences. 

Cet article présente les principaux concepts et fonctionnalités d’Azure Database pour PostgreSQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Consultez ces démarrages rapides pour bien commencer :

- [Création d’une instance d’Azure Database pour PostgreSQL à l’aide du portail Azure](quickstart-create-server-database-portal.md)
- [Création d’une instance d’Azure Database pour PostgreSQL à l’aide de la CLI Azure](quickstart-create-server-database-azure-cli.md)

Pour accéder à des exemples Azure CLI et PowerShell, consultez :

- [Exemples Azure CLI pour base de données pour PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustez les performances et la mise à l'échelle sans interruption de service

Azure Database pour le service PostgreSQL propose actuellement deux niveaux de service : De base et Standard. Chaque niveau de service offre [différents niveaux de performances, de garanties d’E/S par seconde et de fonctionnalités](concepts-service-tiers.md) pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur un petit serveur pour quelques euros par mois et [modifier le niveau de performances](scripts/sample-scale-server-up-or-down.md) au sein du service manuellement ou automatiquement à tout moment pour répondre aux besoins de votre solution. Cette opération ne perturbe pas le fonctionnement de votre application, ni l’expérience de vos clients. L’évolutivité dynamique permet à votre base de données de répondre en toute transparence aux besoins en ressources qui évoluent sans cesse et de payer uniquement les ressources dont vous avez besoin, lorsque vous en avez besoin.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte

Comment savoir quand augmenter ou diminuer la taille des instances ? Vous utilisez les fonctionnalités intégrées de surveillance et d’alerte de performances, combinées avec les évaluations de performance basées sur les unités de calcul. Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle d’unités de calcul (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. Pour plus de détails, consultez [Options et performances d’Azure Database pour PostgrSQL : comprendre ce qui est disponible dans chaque niveau de service](./concepts-service-tiers.md).

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais

Avec un temps de disponibilité de 99,99 % (non disponible dans la version préliminaire), l’excellent contrat de niveau de service (SLA) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque serveur Azure Database pour PostgreSQL, vous tirez parti de la sécurité intégrée, d’une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Avec Azure Database pour PostgreSQL, chaque niveau de service offre un ensemble complet de fonctionnalités et d’options assurant la continuité des activités, que vous pouvez utiliser pour démarrer votre base de données et vous assurer qu’elle fonctionne de manière continue. Vous pouvez utiliser la [limite de restauration dans le temps](howto-restore-server-portal.md) pour renvoyer une base de données à un état antérieur, jusqu’à 35 jours. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez restaurer les bases de données à partir de copies géo-redondantes des sauvegardes récentes.

## <a name="secure-your-data"></a>Sécurisez vos données

Les services de base de données Azure ont une tradition de sécurité des données qu’Azure Database pour PostgreSQL respecte avec des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement, et vous aident à surveiller l’activité. Visitez le [Centre de gestion de la confidentialité Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx) pour plus d’informations sur la sécurité de la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes
- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/postgresql/) pour des comparaisons de coûts et des calculatrices.
- Commencez par [créer votre première instance d’Azure Database pour PostgreSQL](./quickstart-create-server-database-portal.md).
- Créez votre première application Python, PHP, Ruby, C\#, Java, Node.js : [bibliothèques de connexions](./concepts-connection-libraries.md)

