---
title: "Présentation de Web Apps | Microsoft Docs"
description: "Découvrez comment Azure App Service vous aide à développer et héberger des applications web."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 18959934c53e2e1c719cc627ffa286acbdcaa967


---
# <a name="web-apps-overview"></a>Vue d'ensemble de Web Apps
*App Service Web Apps* est une plateforme de calcul entièrement gérée, optimisée pour l’hébergement de sites et d’applications web. Cette offre [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (Plateforme en tant que service) de Microsoft Azure vous permet de donner la priorité à votre logique métier tandis qu’Azure gère l’infrastructure servant à exécuter et à faire évoluer vos applications.

La vidéo suivante de 5 minutes présente Azure App Service Web Apps.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>Qu’est-ce qu’une application web dans App Service ?
Dans App Service, une *application web* constitue les ressources de calcul fournies par Azure pour l’hébergement d’un site ou d’une application web.  

Les ressources de calcul peuvent se trouver sur des machines virtuelles partagées ou dédiées, selon le niveau tarifaire que vous choisissez. Le code de votre application s’exécute sur une machine virtuelle isolée des autres clients.

Votre code peut être écrit dans n’importe quel langage ou infrastructure pris en charge par [Azure App Service](../app-service/app-service-value-prop-what-is.md), tels que ASP.NET, Node.js, Java, PHP ou Python. Vous pouvez également exécuter des scripts utilisant [PowerShell et d’autres langages de script](web-sites-create-web-jobs.md#acceptablefiles) dans une application web.

Pour obtenir des exemples de scénarios d’application standard pour lesquels la fonctionnalité Web Apps peut être utilisée, consultez différents [scénarios Web Apps](https://azure.microsoft.com/documentation/scenarios/web-app/) et accédez à la section **Scénarios et recommandations** de l’article [Comparaison entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Pourquoi utiliser Web Apps ?
Voici les principales fonctionnalités du service d’application qui s’appliquent aux applications web :

* **Plusieurs langages et infrastructures** : App Service offre une excellente prise en charge d’ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [PowerShell et d’autres scripts ou exécutables](web-sites-create-web-jobs.md) sur les machines virtuelles App Service.
* **Optimisation DevOps** : configurez [l’intégration et le déploiement continus](app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Assurez la promotion des mises à jour par le biais des [environnements de test et intermédiaires](web-sites-staged-publishing.md). Effectuez des [tests A/B](app-service-web-test-in-production-get-start.md). Gérez vos applications dans App Service à l’aide d’[Azure PowerShell](/powershell/azureps-cmdlets-docs) ou de la [CLI interplateforme](../xplat-cli-install.md).
* **Mise à l’échelle globale avec une haute disponibilité** : effectuez des [montées en puissance](web-sites-scale.md) ou [augmentez la taille des instances](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Hébergez vos applications n’importe où dans l’infrastructure mondiale des centres de données de Microsoft, et bénéficiez des garanties du [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) App Service en matière de haute disponibilité.
* **Connexion aux plateformes SaaS et données locales** : choisissez parmi plus de 50 [connecteurs](../connectors/apis-list.md) pour des systèmes d’entreprise tels que SAP, Siebel et Oracle, des services SaaS tels que Salesforce et Office 365 et des services Internet tels que Facebook et Twitter. Accédez aux données locales à l’aide de [connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md) et de [réseaux virtuels Azure](web-sites-integrate-with-vnet.md).
* **Sécurité et conformité** : App Service est [conforme aux normes ISO, SOC et PCI](https://www.microsoft.com/TrustCenter/).
* **Modèles d’application** : faites votre choix parmi une liste complète de modèles d’application dans [Azure Marketplace](https://azure.microsoft.com/marketplace/) , qui vous permettent d’utiliser un Assistant pour installer des logiciels open source populaires tels que WordPress, Joomla et Drupal.
* **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.

En outre, une application web peut tirer parti des fonctionnalités offertes par [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (comme la prise en charge de CORS) et [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (comme les notifications Push). Pour plus d’informations sur les types d’application dans App Service, consultez l’article [Qu’est-ce qu’Azure App Service ?](../app-service/app-service-value-prop-what-is.md)

En plus des applications web dans App Service, Azure offre d’autres services qui peuvent être utilisés pour l’hébergement de sites et d’applications web. Pour la plupart des scénarios, Web Apps est le meilleur choix.  Pour l’architecture basée sur des microservices, envisagez l’utilisation de [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) ; si vous avez besoin de contrôler davantage les machines virtuelles sur lesquelles votre code s’exécute, utilisez plutôt [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Pour plus d’informations sur le choix entre ces services Azure, consultez [Comparaison entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Prise en main
Pour démarrer en déployant un exemple de code vers une nouvelle application web dans App Service, suivez un des didacticiels de la liste déroulante suivante. Vous devrez créer un compte Azure gratuit

> [!div class="op_single_selector"]
> * [Déployer votre premier site HTML dans Azure en 5 minutes](app-service-web-get-started-html-cli-nodejs.md)
> * [Déployer votre première application web ASP.NET dans Azure en 5 minutes](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [Déployer votre première application web PHP dans Azure en 5 minutes](app-service-web-get-started-php-cli-nodejs.md)
> * [Déployer votre première application web Node.js dans Azure en 5 minutes](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [Déployer votre première application web Python dans Azure en 5 minutes](app-service-web-get-started-python-cli-nodejs.md)
> * [Déployer votre première application web Java dans Azure en 5 minutes](app-service-web-get-started-java.md)
> 
> 

> [!NOTE]
> Vous pouvez [essayer App Service](https://azure.microsoft.com/try/app-service/) sans compte Azure. Créez une application de base et expérimentez-la pendant une heure, sans carte de paiement et sans engagement.
> 
> 



<!--HONumber=Feb17_HO2-->


