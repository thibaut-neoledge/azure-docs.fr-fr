---
title: "Prise en main du conseiller Azure | Microsoft Docs"
description: Prise en main du conseiller Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Prise en main d’Azure Advisor

Découvrez comment accéder à Advisor à l’aide du portail Azure, et comment obtenir, implémenter, rechercher et actualiser des recommandations.

## <a name="get-advisor-recommendations"></a>Obtenir des recommandations du conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le volet gauche, cliquez sur **Autres services**.

3. Dans le volet du menu de services, sous **Surveillance et gestion**, cliquez sur **Azure Advisor**.  
 Le tableau de bord Advisor s’affiche.

   ![Accéder au conseiller Azure avec le portail Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. Dans le tableau de bord d’Advisor, sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations.  
Le tableau de bord du conseiller affiche des recommandations personnalisées pour un abonnement sélectionné. 

5. Pour obtenir des recommandations pour une catégorie particulière, cliquez sur l’un des onglets **Haute disponibilité**, **Sécurité**, **Performances** ou **Coût**.
 
> [!NOTE]
> Pour accéder aux recommandations d’Advisor, vous devez d’abord *inscrire votre abonnement*  auprès d’Advisor. L’abonnement est inscrit lorsque son *propriétaire* lance le tableau de bord Advisor, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être *exécutée qu’une seule fois*. Une fois l’abonnement inscrit, vous pouvez accéder aux recommandations d’Advisor en tant que *propriétaire*, *collaborateur* ou *lecteur* d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

  ![Tableau de bord du conseiller Azure](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Obtenir les détails de recommandation d’Advisor et implémenter une solution

Le panneau **Recommandation** d’Advisor offre des informations supplémentaires sur la recommandation. 

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis lancez [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Dans le tableau de bord **Recommandations du conseiller**, cliquez sur **Obtenir des recommandations**.

3. Dans la liste des recommandations, cliquez sur une recommandation que vous souhaitez examiner en détail.  
Le panneau **Recommandation** s’affiche.

4. Dans le panneau **Recommandations**, passez en revue les informations sur les actions que vous pouvez effectuer pour résoudre un problème potentiel ou tirer parti d’une opportunité d’économies. 
  
  ![Panneau des recommandations d’Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Recherchez des recommandations du conseiller

Vous pouvez rechercher des recommandations pour un abonnement ou un groupe de ressources spécifiques. Vous pouvez également rechercher des recommandations par état.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis lancez [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Recherchez des recommandations en filtrant sur les abonnements, les groupes de ressources et les états de recommandation (**Active** ou **Répétée**).

3. Cliquez sur **Obtenir des recommandations** pour obtenir une liste de recommandations d’Advisor en fonction de vos filtres de recherche.

  ![Critères de filtre de recherche d’Advisor](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Répéter ou ignorer les recommandations d’Advisor

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis lancez [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Cliquez sur **Obtenir des recommandations**, puis dans la liste des recommandations, cliquez sur une recommandation.

3. Dans le panneau **Recommandations**, cliquez sur **Répéter**.  

   ![Exemple d’action recommandée du conseiller](./media/advisor-get-started/advisor-snooze.png)

4. Spécifiez une période de répétition ou sélectionnez **Jamais** pour faire disparaître la recommandation.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Advisor, consultez les ressources suivantes :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
-  [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
