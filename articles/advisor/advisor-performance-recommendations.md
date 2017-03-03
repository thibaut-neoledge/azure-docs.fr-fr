---
title: "Recommandations du conseiller Azure en matière de performances | Microsoft Docs"
description: "Utilisez le conseiller pour optimiser les performances de vos déploiements Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: d04c209894ab8ee35b92381f9679f68addd64f00
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-performance-recommendations"></a>Recommandations du conseiller en matière de performances

Les recommandations du conseiller en matière de performances vous aident à optimiser la vitesse et la réactivité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de performances à l’aide du conseiller à partir de l’onglet **Performances** du tableau de bord du conseiller.

![Onglet Performances du conseiller](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>Recommandations SQL Database

Le conseiller vous offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure. Il s’intègre à SQL Database Advisor pour vous proposer des recommandations en vue d’améliorer les performances de votre base de données SQL Azure. SQL Database Advisor évalue les performances de vos bases de données SQL Azure en analysant votre historique d’utilisation. Il propose alors les recommandations les plus adaptées pour exécuter la charge de travail standard de votre base de données. 

> [!NOTE]
> Pour obtenir des recommandations, une base de données doit avoir été utilisée pendant environ une semaine et avoir fait l’objet d’une activité cohérente au cours de cette semaine. SQL Database Advisor peut plus facilement optimiser des modèles de requête cohérents que des pics d’activité aléatoires.

Pour plus d’informations sur SQL Database Advisor, consultez la page [SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Recommandations SQL Database](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Recommandations de Cache Redis

Le conseiller Azure identifie les instances de Cache Redis où les performances peuvent être défavorablement affectées par une utilisation intensive de la mémoire, la charge du serveur, la bande passante réseau ou un grand nombre de connexions client. Il fournit également des recommandations quant aux meilleures pratiques pour éviter les problèmes potentiels. Pour plus d’informations concernant les recommandations de Cache Redis, consultez [Redis Cache Advisor](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="app-services-recommendations"></a>Recommandations App Services

Le conseiller Azure intègre des recommandations quant aux meilleures pratiques pour améliorer votre expérience App Services et vous faire découvrir des fonctionnalités appropriées de la plateforme. Exemples de recommandations App Services :
- détection des instances où les ressources de mémoire ou de processeur sont épuisées par des exécutions d’application avec options d’atténuation
- détection des instances où la colocalisation de ressources telles que les applications web et les bases de données peut améliorer les performances et réduire les coûts. 

Pour plus d’informations sur les recommandations App Services, consultez [Meilleures pratiques pour Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Recommandations App Services](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de performances dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, cliquez sur **Plus de services**, dans le volet de menu service, faites défiler jusqu’à **Surveillance et gestion**, puis cliquez sur **Conseiller Azure**. Cette opération permet de lancer le tableau de bord du conseiller. 
3. Dans le tableau de bord du conseiller, cliquez sur l’onglet **Performances**, et sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations.

> [!NOTE]
> Pour accéder aux recommandations du conseiller, vous devez d’abord **enregistrer** votre abonnement avec auprès du conseiller. Un abonnement est enregistré lorsque son **propriétaire** lance le tableau de bord du conseiller, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être **exécutée qu’une seule fois**. Lorsqu’un abonnement est enregistré, les recommandations du conseiller peuvent être consultées par les **propriétaire**s, **collaborateur**s, ou **lecteur**s d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations du conseiller, consultez les ressources suivantes :

-  [Présentation du conseiller](advisor-overview.md)
-  [Prise en main du conseiller](advisor-get-started.md)
-  [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
-  [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
-  [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)

