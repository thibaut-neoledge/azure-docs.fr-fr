---
title: "Recommandations de sécurité du conseiller Azure | Microsoft Docs"
description: "Utilisez le conseiller Azure pour améliorer la sécurité de vos déploiements Azure."
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
ms.openlocfilehash: dbdbae0f87d8d115407026b263c65c3b387fa178
ms.lasthandoff: 02/21/2017

---
# <a name="advisor-security-recommendations"></a>Recommandations de sécurité du conseiller

Le conseiller vous offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure. Il s’intègre avec Azure Security Center pour vous proposer des recommandations de sécurité. Vous pouvez obtenir des recommandations en matière de sécurité à partir de l’onglet **Sécurité** du tableau de bord du conseiller.

![Onglet Sécurité du conseiller](./media/advisor-security-recommendations/advisor-security-tab.png)

Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires. 

![Onglet Sécurité du conseiller](./media/advisor-security-recommendations/advisor-security-recommendations.png)

Pour plus d’informations sur les recommandations de sécurité, consultez [Gestion des recommandations de sécurité dans Azure Security Center](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Accès aux recommandations de sécurité dans le conseiller Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, cliquez sur **Plus de services**, dans le volet de menu service, faites défiler jusqu’à **Surveillance et gestion**, puis cliquez sur **Conseiller Azure**. Cette opération permet de lancer le tableau de bord du conseiller. 
3. Dans le tableau de bord du conseiller, cliquez sur l’onglet **Sécurité**, et sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations.

> [!NOTE]
> Pour accéder aux recommandations du conseiller, vous devez d’abord **enregistrer** votre abonnement avec auprès du conseiller. Un abonnement est enregistré lorsque son **propriétaire** lance le tableau de bord du conseiller, puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être **exécutée qu’une seule fois**. Lorsqu’un abonnement est enregistré, les recommandations du conseiller peuvent être consultées par les **propriétaire**s, **collaborateur**s, ou **lecteur**s d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations du conseiller, consultez les ressources suivantes :
-  [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
-  [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
-  [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
 

