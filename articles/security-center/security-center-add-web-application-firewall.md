---
title: "Ajouter un pare-feu d&quot;applications web dans le Centre de sécurité Azure | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation d’Azure Security Center **Add a web application firewall** (Ajouter un pare-feu d’applications web) et **Finalize application protection** (Finaliser la protection des applications)."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2286437f4ab13384f895e906ccda48ac1b4c553d
ms.openlocfilehash: b44a0373ceca84b423984e01eee1e57a67d97cdd


---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Ajouter un pare-feu d'applications web dans le Centre de sécurité Azure
Azure Security Center peut vous recommander d'ajouter un pare-feu d'applications web (WAF) d'un partenaire Microsoft afin de sécuriser vos applications web. Ce document inclut un exemple vous expliquant comment procéder pour appliquer cette recommandation.

Une recommandation WAF est indiquée pour n’importe quelle IP publique (adresse IP de niveau d’instance ou adresse IP à équilibrage de charge) ayant un groupe de sécurité réseau associé avec des ports web entrants ouverts (80, 443).

Security Center recommande d’approvisionner un WAF pour vous défendre contre les attaques ciblant vos applications web sur les machines virtuelles et sur l’environnement App Service (ASE). Un environnement App Service (ASE) est une option de plan de service [Premium](https://azure.microsoft.com/pricing/details/app-service/) d'Azure App Service qui fournit un environnement totalement isolé et dédié pour l'exécution sécurisée de vos applications Azure App Service. Pour en savoir plus sur ASE, voir [Documentation sur l’environnement App Service](../app-service/app-service-app-service-environments-readme.md).

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n'est pas un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Sécuriser l’application web à l’aide du pare-feu d’applications web**.
   ![Sécuriser l’application web][1]
2. Dans le panneau **Sécuriser l’application web à l’aide du pare-feu d’applications web** , sélectionnez une application web. Le panneau **Ajouter un pare-feu d’applications web** s’ouvre.
   ![Add a web application firewall][2]
3. Vous pouvez choisir d'utiliser un pare-feu d'applications web existant s'il est disponible, ou en créer un. Dans cet exemple, il n'existe aucun pare-feu WAF existant, et nous allons donc en créer un.
4. Pour créer un pare-feu WAF, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous sélectionnons **Pare-feu d’applications web Barracuda**.
5. Le panneau **Pare-feu d’applications web Barracuda** s’ouvre et affiche des informations sur la solution du partenaire. Sélectionnez **Créer** dans le panneau d’informations.
   ![Panneau d’informations du pare-feu][3]
6. Le panneau **Nouveau pare-feu d’applications web** s’ouvre. Vous pouvez y **configurer des machines virtuelles** et fournir des **informations sur le pare-feu d’applications web**. Sélectionnez **Configuration de machine virtuelle**.
7. Dans le panneau **Configuration de machine virtuelle**, entrez les informations nécessaires pour créer la machine virtuelle qui exécute le pare-feu d’applications web.
   ![VM configuration][4]
8. Retournez au panneau **Nouveau pare-feu d’applications web**, puis sélectionnez **Informations sur le pare-feu d’applications web**. Dans le panneau **Informations sur le pare-feu d’applications web**, vous pouvez configurer le pare-feu d’applications web. L’étape 7 vous permet de configurer la machine virtuelle sur laquelle le pare-feu d’applications web s'exécute, et l’étape 8 vous permet d’approvisionner le pare-feu d’applications web.

## <a name="finalize-application-protection"></a>Finaliser la protection des applications
1. Retournons au panneau **Recommandations** . Une entrée a été générée après la création du pare-feu d’applications web, appelée **Finaliser la protection des applications**. Cela vous indique que vous devez terminer le processus de configuration du pare-feu d’applications web dans le réseau virtuel Azure pour qu’il puisse protéger l’application.
   ![Finaliser la protection des applications][5]
2. Sélectionnez **Finaliser la protection des applications**. Un nouveau panneau s'ouvre. Vous voyez que le trafic d’une application web doit être redirigé.
3. Sélectionnez l’application web. Un panneau s’ouvre et affiche des instructions pour finaliser la configuration du pare-feu d’applications web. Suivez les étapes, puis sélectionnez **Restreindre le trafic**. Azure Security Center effectue ensuite la configuration à votre place.
   ![Restreindre le trafic][6]

> [!NOTE]
> Vous pouvez protéger plusieurs applications web dans le centre de sécurité en les ajoutant à vos déploiements WAF existants.
>
>

Les journaux du pare-feu d’applications web sont maintenant entièrement intégrés. Le Centre de sécurité peut commencer automatiquement à collecter et à analyser les journaux afin de vous informer des alertes de sécurité les plus importantes.

## <a name="see-also"></a>Voir aussi
Ce document vous a montré comment implémenter la recommandation du Centre de sécurité « Add a web application » (Ajouter une application web). Pour en savoir plus sur la configuration d'un pare-feu d'applications web, consultez les rubriques suivantes :

* [Configuration d'un pare-feu d'applications Web (WAF) pour un environnement App Service](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png



<!--HONumber=Feb17_HO3-->


