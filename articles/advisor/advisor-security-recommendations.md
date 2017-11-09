---
title: "Recommandations de sécurité du conseiller Azure | Microsoft Docs"
description: "Utilisez le conseiller Azure pour améliorer la sécurité de vos déploiements Azure."
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
ms.openlocfilehash: bb690bf86743136778e01b480b98c9cc08211839
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-security-recommendations"></a>Recommandations de sécurité du conseiller

Azure Advisor vous offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure. Il s’intègre avec Azure Security Center pour vous proposer des recommandations de sécurité. Vous pouvez obtenir des recommandations en matière de sécurité à partir de l’onglet **Sécurité** du tableau de bord Advisor.

![Bouton Sécurité Advisor](./media/advisor-security-recommendations/advisor-security-tab.png)

Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires. 

![Onglet Sécurité Advisor](./media/advisor-security-recommendations/advisor-security-recommendations.png)

Pour plus d’informations sur les recommandations de sécurité, consultez [Gestion des recommandations de sécurité dans Azure Security Center](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/).

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Accès aux recommandations de sécurité dans Azure Advisor

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le volet gauche, cliquez sur **Autres services**.

3. Dans le volet du menu de services, sous **Surveillance et gestion**, cliquez sur **Azure Advisor**.  
 Le tableau de bord Advisor s’affiche.

4. Dans le tableau de bord Advisor, cliquez sur l’onglet **Sécurité**.

5. Sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations, puis cliquez sur **Obtenir des recommandations**.

> [!NOTE]
> Pour accéder aux recommandations d’Advisor, vous devez d’abord *inscrire votre abonnement*  auprès d’Advisor. L’abonnement est inscrit lorsque son *propriétaire* lance le tableau de bord Advisor, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être *exécutée qu’une seule fois*. Une fois l’abonnement inscrit, vous pouvez accéder aux recommandations d’Advisor en tant que *propriétaire*, *collaborateur* ou *lecteur* d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :
* [Présentation d’Azure](advisor-overview.md)
* [Prise en main du conseiller](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)


 
