---
title: "Présentation d’Azure Advisor | Microsoft Docs"
description: "Le conseiller Azure permet d’optimiser vos déploiements Azure."
services: advisor
documentationcenter: NA
author: KumudD
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
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Présentation d’Azure Advisor

Découvrez Azure Advisor et ses principales fonctionnalités et obtenez des réponses aux questions fréquemment posées.

## <a name="what-is-advisor"></a>Qu’est-ce qu’Advisor ?
Advisor est un conseiller personnalisé basé dans le cloud qui décrit les meilleures pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

Avec Advisor, vous pouvez :
* Bénéficier de recommandations en termes de meilleures pratiques proactives, interactives et personnalisées. 
* Améliorer les performances, la sécurité et la haute disponibilité de vos ressources en identifiant les possibilités de réduction de vos dépenses Azure globales.
* Obtenir des recommandations avec des propositions d’actions intégrées.

Accéder à Advisor via le [portail Azure](https://aka.ms/azureadvisordashboard). Connectez-vous au [portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu’à **Azure Advisor**. Le tableau de bord du conseiller affiche des recommandations personnalisées pour un abonnement sélectionné. 

Les recommandations sont divisées en quatre catégories : 

* **Haute disponibilité** : pour garantir et améliorer la continuité de vos applications stratégiques. Pour plus d’informations, consultez [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md).

* **Sécurité** : permet de détecter les menaces et vulnérabilités pouvant conduire à des failles de sécurité. Pour plus d’informations, consultez [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md).

* **Performances** : pour améliorer la vitesse de vos applications. Pour plus d’informations, consultez [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md).

* **Coût** : pour optimiser et réduire vos dépenses Azure globales. Pour plus d’informations, consultez [Recommandations du conseiller en matière de coût](advisor-cost-recommendations.md).

  ![Types de recommandation du conseiller](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Pour accéder aux recommandations d’Advisor, vous devez d’abord *inscrire votre abonnement*  auprès d’Advisor. L’abonnement est inscrit lorsque son *propriétaire* lance le tableau de bord Advisor, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être *exécutée qu’une seule fois*. Une fois l’abonnement inscrit, vous pouvez accéder aux recommandations d’Advisor en tant que *propriétaire*, *collaborateur* ou *lecteur* d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

Vous pouvez cliquer sur une recommandation pour en savoir plus. Vous pouvez également obtenir des informations sur les actions que vous pouvez effectuer pour tirer parti d’une opportunité ou résoudre un problème. 

Le conseiller fournit des recommandations accompagnées d’actions intégrées ou de liens vers de la documentation. Cliquez sur une action intégrée pour découvrir comment l’implémenter. Cliquez sur un lien de documentation pour accéder à la documentation qui décrit comment implémenter manuellement l’action. 

Advisor met à jour les recommandations toutes les heures. Si vous ne prévoyez pas d’effectuer d’action immédiate sur une recommandation, vous pouvez la répéter pendant une période spécifique ou la faire disparaître. 

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="how-do-i-access-advisor"></a>Accès au conseiller
Accéder à Advisor via le [portail Azure](https://aka.ms/azureadvisordashboard). Connectez-vous au [portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu’à **Azure Advisor**. Le tableau de bord du conseiller affiche des recommandations personnalisées pour un abonnement sélectionné. 

Vous pouvez également afficher les recommandations du conseiller via le panneau des ressources de la machine virtuelle. Choisissez une machine virtuelle, puis accédez aux recommandations du conseiller dans le menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Quelles autorisations dois-je avoir pour accéder au conseiller ?

Pour accéder aux recommandations d’Advisor, vous devez d’abord *inscrire votre abonnement*  auprès d’Advisor. L’abonnement est inscrit lorsque son *propriétaire* lance le tableau de bord Advisor, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être *exécutée qu’une seule fois*. Une fois l’abonnement inscrit, vous pouvez accéder aux recommandations d’Advisor en tant que *propriétaire*, *collaborateur* ou *lecteur* d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

### <a name="how-often-are-advisor-recommendations-updated"></a>A quelle fréquence les recommandations du conseiller sont-elles mises à jour ?

Les recommandations d’Advisor sont mises à jour toutes les heures.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Pour quelles ressources le conseiller fournit-il des recommandations ?

Advisor fournit des recommandations pour les machines virtuelles, les groupes à haute disponibilité, les passerelles d’application, App Services, les serveurs SQL, les bases de données SQL et le Cache Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Puis-je répéter ou ignorer une recommandation ?

Pour répéter ou ignorer une recommandation, cliquez sur le bouton ou le lien **Répéter**. Vous pouvez spécifier une période de répétition ou sélectionner **Jamais** pour faire disparaître la recommandation.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :

* [Prise en main du conseiller](advisor-get-started.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)
