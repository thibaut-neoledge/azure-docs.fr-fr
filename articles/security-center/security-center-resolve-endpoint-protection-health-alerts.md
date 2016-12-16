---
title: "Résoudre les alertes d’intégrité Endpoint Protection dans Azure Security Center| Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation du Centre de sécurité Azure **Résoudre les alertes d’intégrité Endpoint Protection**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4050f453-98fc-4314-8438-d476469757fb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e6b136d6bd3b11fb82126d104fd0cb149255118


---
# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Résoudre les alertes d’intégrité Endpoint Protection dans le Centre de sécurité Azure
Le Centre de sécurité Azure recommande de résoudre les alertes d’intégrité Endpoint Protection.  Le Centre de sécurité vous permet d’identifier les machines virtuelles qui rencontrent des défaillances Endpoint Protection et d’afficher le nombre de ces défaillances.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
> 
> 

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Résoudre les alertes d’intégrité Endpoint Protection**.
   ![Résoudre les alertes d’intégrité Endpoint Protection][1]
2. Cette opération ouvre le panneau **Échec de la protection de point de terminaison** , qui répertorie les machines virtuelles défaillantes ainsi que le nombre de défaillances pour chaque machine virtuelle. Sélectionnez une machine virtuelle dans la liste.
   ![Endpoint protection failure][2]
3. Un panneau **Liste des échecs** affiche la liste des défaillances pour la machine virtuelle sélectionnée. Sélectionnez une défaillance dans la liste pour en savoir plus. Un panneau s’ouvre et affiche les informations sur la défaillance sélectionnée.
   ![Liste des échecs][3]
   ![Événement d’échec][4]

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur Security Center, consultez :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md): découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md): découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md): découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ du Centre de sécurité Azure](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png



<!--HONumber=Nov16_HO3-->


