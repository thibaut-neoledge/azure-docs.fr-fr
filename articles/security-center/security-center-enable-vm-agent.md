---
title: "Activer l’agent de machine virtuelle dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation du Centre de sécurité Azure **Activer l’agent de machine virtuelle**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2084bce2529aaf65194f69b463cb0a1b480e5769


---
# <a name="enable-vm-agent-in-azure-security-center"></a>Activer l’agent de machine virtuelle dans le Centre de sécurité Azure
L’agent de machine virtuelle doit être installé sur les machines virtuelles pour [activer la collecte des données](security-center-enable-data-collection.md).  Le Centre de sécurité Azure vous permet de voir quelles machines virtuelles nécessitent l’agent de machine virtuelle et vous recommandera d’activer l’agent sur ces machines virtuelles.

L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
> 
> 

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations** , sélectionnez **Activer l’agent de machine virtuelle**.
   ![Activer l’agent de machine virtuelle][1]
2. Cette opération ouvre le panneau **L’agent de machine virtuelle est manquant ou ne répond pas**. Ce panneau répertorie les machines virtuelles qui nécessitent l’agent de machine virtuelle. Suivez les instructions dans le panneau pour installer l’agent de machine virtuelle.
   ![L’agent de machine virtuelle est manquant][2]

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
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png



<!--HONumber=Nov16_HO3-->


