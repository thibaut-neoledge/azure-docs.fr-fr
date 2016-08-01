<properties
   pageTitle="Activer l’agent de machine virtuelle dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du Centre de sécurité Azure **Activer l’agent de machine virtuelle**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Activer l’agent de machine virtuelle dans le Centre de sécurité Azure

L’agent de machine virtuelle doit être installé sur les machines virtuelles pour approvisionner l’analyse des correctifs, l’analyse des lignes de base et les logiciels anti-programme malveillant. Le Centre de sécurité Azure vous permet de voir quelles machines virtuelles nécessitent l’agent de machine virtuelle et vous recommandera d’activer l’agent sur ces machines virtuelles.

L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle.


> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Implémenter la recommandation

1. Dans le panneau **Recommandations** , sélectionnez **Activer l’agent de machine virtuelle**. ![Activer l’agent de machine virtuelle][1]

2. Cette opération ouvre le panneau **L’agent de machine virtuelle est manquant ou ne répond pas**. Ce panneau répertorie les machines virtuelles qui nécessitent l’agent de machine virtuelle. Suivez les instructions dans le panneau pour installer l’agent de machine virtuelle. ![L’agent de machine virtuelle est manquant][2]

## Voir aussi

Pour plus d’informations sur Security Center, consultez :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
- [FAQ du Centre de sécurité Azure](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png

<!---HONumber=AcomDC_0720_2016-->