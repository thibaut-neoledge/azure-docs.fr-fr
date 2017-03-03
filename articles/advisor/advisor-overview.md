---
title: "Présentation d’Azure Advisor | Microsoft Docs"
description: "Le conseiller Azure permet d’optimiser vos déploiements Azure."
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
ms.openlocfilehash: 3c4f6d13dd43ae557b177883e14044e637bd803b
ms.lasthandoff: 02/21/2017

---

# <a name="introduction-to-azure-advisor"></a>Présentation d’Azure Advisor

Découvrez Azure Advisor, ses principales fonctionnalités et les questions fréquemment posées.

## <a name="what-is-azure-advisor"></a>Présentation d’Azure Advisor
Azure Advisor est un conseiller personnalisé basé dans le cloud qui décrit les meilleures pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources et recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

Avec Azure Advisor, vous pouvez :
-    bénéficier des recommandations en termes de meilleures pratiques proactives, interactives et personnalisées. 
-    améliorer les performances, la sécurité et la haute disponibilité de vos ressources tout en réduisant vos dépenses Azure globales.
-    obtenir des recommandations accompagnées d’actions intégrées

Accéder à Advisor via le [portail Azure](https://aka.ms/azureadvisordashboard). Connectez-vous au [portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu’à **Azure Advisor**). Le tableau de bord du conseiller affiche des recommandations personnalisées pour un abonnement sélectionné. Les recommandations sont divisées en quatre catégories. 

-   **Haute disponibilité** – pour garantir et améliorer la continuité de vos applications stratégiques. Pour plus d’informations, consultez [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md).

-   **Sécurité** : permet de détecter les menaces et vulnérabilités pouvant conduire à des failles de sécurité potentielles. Pour plus d’informations, consultez [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md).

-   **Performances** : pour améliorer la vitesse de vos applications. Pour plus d’informations, consultez [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md).

-   **Coût** : pour optimiser et réduire vos dépenses Azure globales. Pour plus d’informations, consultez [Recommandations du conseiller en matière de coût](advisor-cost-recommendations.md).

  ![Types de recommandation du conseiller](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Pour accéder aux recommandations du conseiller, vous devez d’abord **enregistrer** votre abonnement avec auprès du conseiller. Un abonnement est enregistré lorsque son **propriétaire** lance le tableau de bord du conseiller, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être **exécutée qu’une seule fois**. Lorsqu’un abonnement est enregistré, les recommandations du conseiller peuvent être consultées par les **propriétaire**s, **collaborateur**s, ou **lecteur**s d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

Vous pouvez cliquer sur une recommandation pour prendre connaissance des informations supplémentaires. Vous pouvez également obtenir des informations sur les actions que vous pouvez effectuer pour tirer parti d’une opportunité ou résoudre un problème. Le conseiller fournit des recommandations accompagnées d’actions intégrées ou de liens vers de la documentation. Cliquez sur une action intégrée pour découvrir comment l’implémenter. Cliquez sur un lien de documentation pour accéder à la documentation qui décrit comment implémenter manuellement l’action. 

Le conseiller met à jour les recommandations toutes les heures. Si vous ne prévoyez pas d’effectuer d’action immédiate sur une recommandation, vous pouvez la répéter pendant une période ou la faire disparaître. 

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="how-do-i-access-advisor"></a>Accès au conseiller
Vous pouvez accéder à Advisor via le portail Azure. Connectez-vous au portail, sélectionnez **Parcourir**, puis faites défiler jusqu’à **Azure Advisor**). Vous pouvez également afficher les recommandations du conseiller via le panneau des ressources de la machine virtuelle. Choisissez une machine virtuelle, puis accédez aux recommandations du conseiller dans le menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Quelles autorisations dois-je avoir pour accéder au conseiller ?

Pour accéder aux recommandations du conseiller, vous devez d’abord **enregistrer** votre abonnement avec auprès du conseiller. Un abonnement est enregistré lorsque son propriétaire lance le tableau de bord du conseiller, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être **exécutée qu’une seule fois**. Lorsqu’un abonnement est enregistré, les recommandations du conseiller peuvent être consultées par les **propriétaire**s, **collaborateur**s, ou **lecteur**s d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

### <a name="how-often-are-advisor-recommendations-updated"></a>A quelle fréquence les recommandations du conseiller sont-elles mises à jour ?

Les recommandations du conseiller sont mises à jour toutes les heures.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Pour quelles ressources le conseiller fournit-il des recommandations ?

Le conseiller fournit des recommandations pour les machines virtuelles, les groupes à haute disponibilité, les passerelles d’application, App Services, les serveurs SQL, les bases de données SQL et le cache Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Puis-je répéter ou ignorer une recommandation ?

Pour répéter ou ignorer une recommandation, cliquez sur le bouton ou le lien **Répéter**. Vous pouvez spécifier une période de répétition ou sélectionner **Jamais** pour faire disparaître la recommandation.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations du conseiller, consultez les ressources suivantes :

-  [Prise en main d’Advisor](advisor-get-started.md)
-  [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
-  [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
-  [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
-  [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)

