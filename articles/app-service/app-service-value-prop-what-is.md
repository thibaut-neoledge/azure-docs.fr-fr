---
title: Azure App Service pour les applications web, mobiles et API | Microsoft Docs
description: "Découvrez comment Azure App Service vous permet de développer, déployer et gérer des applications web et mobiles."
keywords: "app service, azure app service, coût de l’app service, mise à l’échelle, évolutif, déploiement d’applications, déploiement d’applications azure, paas, platform-as-a-service, siteweb, site web, azure mobile"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/02/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f361e9c815ae20874fc2e40e39d2c20a415d58f2


---
# <a name="what-is-azure-app-service"></a>Qu'est-ce qu'Azure App Service ?
*App Service* est une offre PaaS ( [platform-as-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service) ) de Microsoft Azure. Créez des applications web et mobiles adaptées à toutes les plateformes et appareils. Intégrez vos applications avec des solutions SaaS, connectez-vous à des applications locales et automatisez vos processus d’entreprise. Azure exécute vos applications sur des machines virtuelles entièrement gérées, avec les ressources de machines virtuelles partagées ou les machines virtuelles dédiées de votre choix.

App Service inclut les fonctionnalités web et mobiles que nous avons précédemment fournies séparément en tant que sites web Azure et services mobiles Azure. Il inclut également de nouvelles fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud. En tant que service intégré unique, App Service vous permet d’assembler différents éléments (sites web, serveurs principaux d’application mobile, API RESTful et processus d’entreprise) au sein d’une solution unique.

La vidéo de 4 minutes suivante explique brièvement la manière dont App Service se rapporte aux offres Azure précédentes, ainsi que ses nouvelles fonctionnalités.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>Pourquoi utiliser App Service ?
Voici quelques fonctionnalités et capacités clés d’App Service :

* **Plusieurs langages et infrastructures** : App Service offre une excellente prise en charge d’ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [Windows PowerShell et d’autres scripts ou exécutables](../app-service-web/web-sites-create-web-jobs.md) sur les machines virtuelles App Service.
* **Optimisation DevOps** : configurez [l’intégration et le déploiement continus](../app-service-web/app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Assurez la promotion des mises à jour par le biais des [environnements de test et intermédiaires](../app-service-web/web-sites-staged-publishing.md). Effectuez des [tests A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Gérez vos applications dans App Service à l’aide d’[Azure PowerShell](/powershell/azureps-cmdlets-docs) ou de la [CLI interplateforme](../xplat-cli-install.md).
* **Mise à l’échelle globale avec une haute disponibilité** : effectuez des [montées en puissance](../app-service-web/web-sites-scale.md) ou [augmentez la taille des instances](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Hébergez vos applications n’importe où dans l’infrastructure mondiale des centres de données de Microsoft, et bénéficiez des garanties du [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) App Service en matière de haute disponibilité.
* **Connexion aux plateformes SaaS et données locales** : choisissez parmi plus de 50 [connecteurs](../connectors/apis-list.md) pour des systèmes d’entreprise tels que SAP, Siebel et Oracle, des services SaaS tels que Salesforce et Office 365 et des services Internet tels que Facebook et Twitter. Accédez aux données locales à l’aide de [connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md) et de [réseaux virtuels Azure](../app-service-web/web-sites-integrate-with-vnet.md).
* **Sécurité et conformité** : App Service est [conforme aux normes ISO, SOC et PCI](https://www.microsoft.com/TrustCenter/).
* **Modèles d’application** : faites votre choix parmi une liste complète de modèles dans [Azure Marketplace](https://azure.microsoft.com/marketplace/) , qui vous permettent d’utiliser un Assistant pour installer des logiciels open source populaires tels que WordPress, Joomla et Drupal.
* **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.

## <a name="app-types-in-app-service"></a>Types d’applications dans App Service
App Service offre plusieurs *types d’application*, chacun d’eux étant destiné à héberger une charge de travail spécifique :

* [**Web Apps**](../app-service-web/app-service-web-overview.md) : pour l’hébergement de sites et d’applications web.
* [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) : pour l’hébergement de serveurs principaux d’application mobile.
* [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) : pour l’hébergement d’API RESTful.
* [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - pour l’automatisation des processus d’entreprise et l’intégration de systèmes et de données dans des clouds sans écrire de code.

Ici, le mot *application* fait référence aux ressources d’hébergements dédiées à l’exécution d’une charge de travail. Par exemple, vous avez certainement l’habitude de considérer qu’une « application web » constitue les ressources de calcul et le code d’application fournissant, ensemble, des fonctionnalités à un navigateur. Cependant, dans App Service, une *application web* constitue les ressources de calcul fournies par Azure pour l’hébergement de votre code d’application. 

Votre application peut être composée d’applications App Service de différents types. Par exemple, si votre application se compose d’un serveur web frontal et d’une API RESTful principale, vous pouvez :

- Déployer les deux (serveur frontal et API) sur une seule application web  
- Déployer votre code frontal dans une application web et votre code principal dans une application API. 



## <a name="app-service-plans"></a>Plans App Service
[Les plans App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) représentent la collection des ressources physiques utilisées pour héberger vos applications.

Les plans App Service définissent :

- **Région** (ouest des États-Unis, est des États-Unis, etc.)
- **Comptage** (un, deux, trois instances, etc.)
- La **taille d’instance** (« Petit », « Moyen », « Grand »)
- **Référence (SKU)** (gratuit, partagé, basique, standard, premium)

Toutes les applications affectées à un **plan App Service** partagent les ressources qu’il définit, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications.

Votre **plan App Service** peut évoluer des références (SKU) **gratuit** ou **partagé** vers **base**, **standard** ou **premium**, ce qui vous donne accès à plus de ressources et de fonctionnalités. Une fois votre plan App Service défini sur **base** ou un plan supérieur, vous pouvez également contrôler la **taille** et faire évoluer le nombre de machines virtuelles.

La **Référence (SKU)** et **l’Échelle** du plan App Service déterminent le coût et non le nombre d’applications hébergées. 

Si vous avez besoin de plus d’évolutivité et d’isolement réseau, vous pouvez exécuter vos applications dans un [environnement App Service](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Tarification
Pour plus d’informations sur le coût d’App Service, consultez la page [Tarification de App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Tester App Service
[Créez un exemple d’application web, mobile ou logique](http://go.microsoft.com/fwlink/?LinkId=523751) et utilisez-la pendant une heure sans carte de crédit, sans aucun engagement et sans aucune contrainte.

Ou ouvrez un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/), puis essayez l’un de nos didacticiels dédiés à la prise en main :

* [Didacticiel : Créer une application web](../app-service-web/app-service-web-get-started.md)
* [Didacticiel : Créer une application mobile](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Didacticiel : Créer une application API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Didacticiel : Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Dec16_HO2-->


