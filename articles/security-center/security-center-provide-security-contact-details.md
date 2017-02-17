---
title: "Fournir les détails du contact de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment fournir les détails du contact de sécurité dans Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f4ae3395c44f175603556711bfe2f90f0f987c94


---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fournir les détails du contact de sécurité dans Azure Security Center
Azure Security Center vous recommande de fournir les détails du contact de sécurité pour votre abonnement Azure, si vous ne l’avez pas déjà fait. Ces informations seront être utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que vos données client ont été utilisées par un tiers illégal ou non autorisé. MSRC procède à une surveillance de la sécurité sur l'infrastructure et le réseau Azure et reçoit des plaintes concernant l’intelligence des menaces et des mauvaises utilisations de tiers.

Une notification par courrier électronique est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Les préférences de courrier électronique peuvent uniquement être configurées pour les stratégies d’abonnement. Les groupes de ressources d’un abonnement hériteront de ces paramètres.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Fournissez les détails du contact de sécurité**.
   ![Fournir le contact de sécurité][1]
2. Le panneau **Fournissez les détails du contact de sécurité**s’ouvre. Sélectionnez l’abonnement Azure pour lequel fournir les informations du contact.
   ![Fournissez les détails du contact de sécurité][2]
3. Un second panneau **Fournissez les détails du contact de sécurité** s’ouvre.

   * Entrez l’adresse e-mail du contact de sécurité. S’il y a plusieurs adresses, séparez-les par des virgules. Vous pouvez entrer le nombre d’adresses e-mail que vous voulez.
   * Entrez le numéro de téléphone international du contact de sécurité.
   * Pour recevoir des courriers électroniques concernant les alertes de gravité élevée, activez l’option **Send me emails about alerts**(M’envoyer des messages électroniques concernant les alertes).
   * À l’avenir, vous aurez la possibilité d’envoyer des notifications par courrier électronique aux propriétaires de l’abonnement. Cette option est actuellement grisée.
   * Sélectionnez **OK** pour appliquer les informations du contact de sécurité à votre abonnement.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png



<!--HONumber=Dec16_HO2-->


