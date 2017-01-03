---
title: "Prise en main du conseiller Azure | Microsoft Docs"
description: Prise en main du conseiller Azure.
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
ms.sourcegitcommit: 14e9e494d6e824964b9164ff36d9938341e244be
ms.openlocfilehash: 7adaa1a457e19c66bdc31a01f4e04b7911e58062

---

# <a name="get-started-with-azure-advisor"></a>Prise en main d’Azure Advisor

Découvrez comment accéder au conseiller à l’aide du portail Azure, ainsi que comment obtenir, mettre en œuvre, rechercher et actualiser des recommandations.

## <a name="get-advisor-recommendations"></a>Obtenir des recommandations du conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, cliquez sur **Plus de services**, puis dans le volet de menu service, faites défiler jusqu’à **Surveillance et gestion**, puis cliquez sur **Conseiller Azure**. Cette opération permet de lancer le tableau de bord du conseiller.

  ![Accéder au conseiller Azure avec le portail Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. Dans le tableau de bord du conseiller, sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations. Le tableau de bord du conseiller affiche des recommandations personnalisées pour un abonnement sélectionné. 
4. Pour obtenir des recommandations pour une catégorie particulière, cliquez sur l’un des onglets **Haute disponibilité**, **Sécurité**, **Performances** ou **Coût**.
 
> [!NOTE]
> Le conseiller Azure génère des recommandations pour les abonnements pour lesquels le rôle de **propriétaire**, de **collaborateur** ou de **lecteur** vous est affecté.

  ![Tableau de bord du conseiller Azure](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>Obtenir les détails de recommandation du conseiller et mettre en œuvre une recommandation

Le panneau **Recommandation** du conseiller offre des informations supplémentaires sur la recommandation du conseiller. 

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis lancez le [conseiller Azure](https://aka.ms/azureadvisordashboard).
2. Dans le tableau de bord **Recommandations du conseiller**, cliquez sur **Obtenir une recommandation**.
3. Dans la liste des recommandations, cliquez sur une recommandation que vous souhaitez examiner en détail. Cette opération lance le panneau Recommandation.
4. Dans le panneau Recommandations, passez en revue les informations sur les actions que vous pouvez effectuer pour résoudre un problème potentiel ou tirer parti d’une opportunité d’économies. 
  
  ![Exemple d’action recommandée du conseiller](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Recherchez des recommandations du conseiller

Vous pouvez rechercher des recommandations pour un abonnement ou un groupe de ressources spécifiques. Vous pouvez également rechercher des recommandations par état.

1. Connectez-vous au portail Azure, puis lancez le conseiller Azure.
2. Recherchez des recommandations en filtrant sur les abonnements, les groupes de ressources et les états de recommandation (**Active** ou **Répétée**).
3. Cliquez sur **Obtenir des recommandations** pour obtenir une liste de recommandations du conseiller en fonction de vos filtres de recherche.

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>Répéter les recommandations du conseiller

1. Connectez-vous au portail Azure, puis lancez le conseiller Azure.
2. Cliquez sur **Obtenir une recommandation**, puis dans la liste des recommandations, cliquez sur une recommandation.
3. Dans le panneau **Recommandations**, cliquez sur **Répéter**.  Vous pouvez spécifier une période de répétition ou sélectionner **Jamais** pour faire disparaître la recommandation.

  ![Exemple d’action recommandée du conseiller](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le conseiller, consultez les ressources suivantes :
-  [Présentation du conseiller Azure](advisor-overview.md)
-  [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
-  [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
-  [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
-  [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->


