---
title: "Recommandations de coûts du conseiller Azure | Microsoft Docs"
description: "Le conseiller Azure permet d’optimiser le coût de vos déploiements Azure."
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
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 39a882a8338ce820c6b9b98f58d1a9db7448c7be

---

# <a name="advisor-cost-recommendations"></a>Recommandations en matière de coûts du conseiller

Le conseiller vous aide à optimiser et à réduire votre dépense Azure globale en identifiant les ressources inactives et sous-utilisées. Vous pouvez obtenir des recommandations en matière de coûts à partir de l’onglet **Coût** du tableau de bord du conseiller.

![Onglet de coût du conseiller](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>Faible utilisation des machines virtuelles 

Alors que certains scénarios d’application peuvent par définition entraîner une faible utilisation, vous pouvez souvent économiser de l’argent grâce à la gestion de la taille des machines virtuelles et de leur nombre. Le conseiller surveille l’utilisation de votre machine virtuelle pendant 14 jours et identifie la faible utilisation des machines virtuelles. Les machines virtuelles dont l’utilisation du processeur est inférieure ou égale à 5 % et celle du réseau est inférieure ou égale à 7 Mo pendant quatre jours ou plus, sont considérées comme des machines virtuelles faiblement utilisées.

Le conseiller vous montre le coût estimatif d’une poursuite de l’exécution de la machine virtuelle. Vous pouvez choisir d’arrêter ou de redimensionner la machine virtuelle.  

![Recommandations de coûts du conseiller pour le redimensionnement des machines virtuelles](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>Recommandations relatives au pool de base de données élastique SQL

Le conseiller identifie les instances de serveur SQL pouvant bénéficier de la création de pools de bases de données élastiques. Les pools de bases de données élastiques offrent une solution simple et économique pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables. Pour de plus amples informations sur les pools élastiques Azure, consultez la page [Qu’est-ce qu’un pool élastique Azure ?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![Recommandations de coût du conseiller pour les pools de bases de données élastiques](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Accès aux recommandations de coût dans le conseiller Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, cliquez sur **Plus de services**, dans le volet de menu service, faites défiler jusqu’à **Surveillance et gestion**, puis cliquez sur **Conseiller Azure**. Cette opération permet de lancer le tableau de bord du conseiller. 
3. Dans le tableau de bord du conseiller, cliquez sur l’onglet **Coût**, sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations, puis cliquez sur **Obtenir des recommandations**

> [!NOTE]
> Le conseiller Azure génère des recommandations pour les abonnements pour lesquels le rôle de **propriétaire**, de **collaborateur** ou de **lecteur** vous est affecté.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations du conseiller, consultez les ressources suivantes :
-  [Présentation d’Azure](advisor-overview.md)
-  [Prise en main](advisor-get-started.md)
-  [Recommandations du conseiller en matière de haute disponibilité](advisor-cost-recommendations.md)
-  [Recommandations du conseiller en matière de sécurité](advisor-cost-recommendations.md)
-  [Recommandations du conseiller en matière de performances](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO3-->


