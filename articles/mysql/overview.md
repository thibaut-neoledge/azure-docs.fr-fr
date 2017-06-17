---
title: "Vue d’ensemble du service de base de données relationnelle d’Azure Database pour MySQL | Microsoft Docs"
description: "Vue d’ensemble du service de base de données relationnelle d’Azure Database pour MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c8d75b0ce8bba4d0c065f86c199538b4a51f4bee
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---


# <a name="what-is-azure-database-for-mysql-service-introduction"></a>Qu’est-ce qu’Azure Database pour MySQL ? Présentation du service
Azure Database pour MySQL est un service de base de données relationnelle dans le cloud de Microsoft basé sur le moteur de base de données [MySQL Community Edition](https://www.mysql.com/products/community/).  Azure Database pour MySQL offre :

- Performances prévisibles sur plusieurs niveaux de service
- Évolutivité dynamique sans interruption de l’application
- Haute disponibilité intégrée
- Protection des données

Ces fonctionnalités ne nécessitent presque aucune administration, et toutes sont fournies sans coût supplémentaire. Elles vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à la gestion des machines virtuelles et de leur infrastructure. En outre, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix et fournir avec la vitesse et l’efficacité que vos activités professionnelles exigent sans avoir à acquérir de nouvelles compétences.

![Diagramme conceptuel d’Azure Database pour MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Cet article présente les principaux concepts et fonctionnalités d’Azure Database pour MySQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Consultez ces démarrages rapides pour bien commencer :
- [Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur Azure Database pour MySQL à l’aide de la CLI Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Pour accéder à des exemples Azure CLI, consultez :
- [Exemples de CLI Azure pour Azure Database pour MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustez les performances et la mise à l'échelle sans interruption de service
Le service Azure Database pour MySQL propose deux niveaux de service : De base et Standard. Chaque niveau offre différentes performances et fonctionnalités pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour quelques dollars par mois, puis changer de niveau de votre service pour vous adapter aux besoins de votre solution sans interruption de service. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin, quand vous en avez besoin.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? Utilisez les fonctionnalités intégrées de surveillance et d’alerte de performances, combinées avec les évaluations de performance basées sur les unités de calcul. Ces fonctionnalités vous permettent d’évaluer rapidement l’impact des mises à l’échelle (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. Consultez [Concepts : niveaux de service](concepts-service-tiers.md) pour plus d’informations.

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service (SLA) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque serveur Azure Database pour MySQL, vous tirez parti de la sécurité intégrée, d’une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Avec Azure Database pour MySQM, vous pouvez utiliser la limite de restauration dans le temps pour récupérer un serveur à un état antérieur, jusqu’à 35 jours.

## <a name="secure-your-data"></a>Sécurisez vos données
Les services de base de données Azure ont une tradition de sécurité des données qu’Azure Database pour MySQL respecte avec des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement, et vous aident à surveiller l’activité. Visitez le [Centre de gestion de la confidentialité Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) pour plus d’informations sur la sécurité de la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu l’introduction à Azure Database pour MySQL et répondu à la question « Qu’est-ce qu’Azure Database pour MySQL ? », vous êtes prêt à effectuer les opérations suivantes :
- Consultez la page de tarification pour des comparaisons de coûts et des calculatrices. [Tarification](https://azure.microsoft.com/pricing/details/mysql/)
- Commencez par créer votre premier serveur. [Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Créer votre première application en Python, PHP, Ruby, C\#, Java, Node.js : [les bibliothèques de connectivité utilisées pour se connecter à Azure Database pour MySQL](concepts-connection-libraries.md)

