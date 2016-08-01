<properties
   pageTitle="Acheminer le trafic via le pare-feu de nouvelle génération uniquement dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du Centre de sécurité Azure **Acheminer le trafic uniquement via un pare-feu de nouvelle génération**."
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

# Acheminer le trafic via le pare-feu de nouvelle génération uniquement dans le Centre de sécurité Azure

Le Centre de sécurité Azure peut détecter si vous avez déployé un pare-feu de nouvelle génération (NGFW). Si vous utilisez des points de terminaison accessibles sur Internet, le Centre de sécurité Azure vous recommandera de configurer des règles de groupe de sécurité réseau qui forcent le trafic entrant vers votre machine virtuelle (VM) via votre pare-feu de nouvelle génération.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Implémenter la recommandation

1. Dans le panneau **Recommandations**, sélectionnez **Acheminer le trafic uniquement via un pare-feu de nouvelle génération**. ![Acheminer le trafic uniquement via un pare-feu de nouvelle génération][1]

2. Cette opération ouvre le panneau **Acheminer le trafic uniquement via un pare-feu de nouvelle génération** qui répertorie les machines virtuelles vers lesquelles vous pouvez acheminer le trafic. Sélectionnez une machine virtuelle dans la liste. ![Sélectionner une machine virtuelle][2]

3. Un panneau pour la machine virtuelle sélectionnée s’ouvre et affiche les règles de trafic entrant associées. Une description vous fournit plus d’informations sur les étapes suivantes possibles. ![Configurer des règles pour limiter l’accès][3]

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
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->