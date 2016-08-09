<properties 
	pageTitle="Azure App Service pour les applications web et mobiles | Microsoft Azure" 
	description="Découvrez comment Azure App Service vous permet de développer, déployer et gérer des applications web et mobiles." 
	keywords="app service, azure app service, coût de l’app service, mise à l’échelle, évolutif, déploiement d’applications, déploiement d’applications azure, paas, platform-as-a-service"
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# Qu'est-ce qu'Azure App Service ?

*App Service* est une offre PaaS ([platform-as-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service)) de Microsoft Azure. Créez des applications web et mobiles adaptées à toutes les plateformes et appareils. Intégrez vos applications avec des solutions SaaS, connectez-vous à des applications locales et automatisez vos processus d’entreprise. Azure exécute vos applications sur des machines virtuelles entièrement gérées, avec les ressources de machines virtuelles partagées ou les machines virtuelles dédiées de votre choix.

App Service inclut les fonctionnalités web et mobiles que nous avons précédemment fournies séparément en tant que sites web Azure et services mobiles Azure. Il inclut également de nouvelles fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud. En tant que service intégré unique, App Service vous permet d’assembler différents éléments (sites web, serveurs principaux d’application mobile, API RESTful et processus d’entreprise) au sein d’une solution unique.

La vidéo de 4 minutes suivante explique brièvement la manière dont App Service se rapporte aux offres Azure précédentes, ainsi que ses nouvelles fonctionnalités.

\+[AZURE.VIDEO app-service-history-lesson] 

## Pourquoi utiliser App Service ?

Voici quelques fonctionnalités et capacités clés d’App Service :

- **Plusieurs langages et infrastructures** : App Service offre une excellente prise en charge d’ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [Windows PowerShell et d’autres scripts ou exécutables](../app-service-web/web-sites-create-web-jobs.md) sur les machines virtuelles App Service.

- **Optimisation DevOps** : configurez [l’intégration et le déploiement continus](../app-service-web/app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Assurez la promotion des mises à jour par le biais des [environnements de test et intermédiaires](../app-service-web/web-sites-staged-publishing.md). Effectuez des [tests A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Gérez vos applications dans App Service à l’aide [d’Azure PowerShell](../powershell-install-configure.md) ou de [l’interface de ligne de commande interplateforme](../xplat-cli-install.md).
 
- **Mise à l’échelle globale avec une haute disponibilité** : effectuez des [montées en puissance](../app-service-web/web-sites-scale.md) ou [augmentez la taille des instances](../azure-portal/insights-how-to-scale.md) manuellement ou automatiquement. Hébergez vos applications n’importe où dans l’infrastructure mondiale des centres de données de Microsoft, et bénéficiez des garanties du [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) App Service en matière de haute disponibilité.

- **Connexion aux plateformes SaaS et données locales** : choisissez parmi plus de 50 [connecteurs](../connectors/apis-list.md) pour des systèmes d’entreprise tels que SAP, Siebel et Oracle, des services SaaS tels que Salesforce et Office 365 et des services Internet tels que Facebook et Twitter. Accédez aux données locales à l’aide de [connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md) et de [réseaux virtuels Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sécurité et conformité** : App Service est [conforme aux normes ISO, SOC et PCI](https://www.microsoft.com/TrustCenter/).

- **Modèles d’application** : faites votre choix parmi une liste complète de modèles d’application dans [Azure Marketplace](https://azure.microsoft.com/marketplace/), qui vous permettent d’utiliser un Assistant pour installer des logiciels open source populaires tels que WordPress, Joomla et Drupal.

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.

## Types d’applications dans App Service

App Service offre plusieurs *types d’application*, chacun d’eux étant destiné à héberger un type spécifique de charge de travail :

- [**Web Apps**](../app-service-web/app-service-web-overview.md) : pour l’hébergement de sites et d’applications web.

- [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) : pour l’hébergement de serveurs principaux d’application mobile.
   
- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) : pour l’hébergement des API cloud.
 
- [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) : pour automatiser l’accès et l’utilisation des données entre des clouds sans écrire de code.

Ici, le mot *application* fait référence aux ressources d’hébergements dédiées à l’exécution d’une charge de travail. Par exemple, vous avez certainement l’habitude de considérer qu’une « application web » constitue les ressources de calcul et le code d’application fournissant, ensemble, des fonctionnalités à un navigateur. Cependant, dans App Service, une *application web* constitue les ressources de calcul fournies par Azure pour l’hébergement de votre code d’application. Si votre application est composée d’un serveur web frontal et d’une API RESTful principale, vous pouvez les déployer sur une application web, ou vous pouvez déployer votre code frontal sur une application web et votre code principal sur une application API. Votre application peut être composée d’applications App Service de différents types.

## Plans App Service

Les [plans App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) spécifient le type des ressources de calcul sur lesquelles vos applications s’exécutent. Si vous envisagez de faibles charges de trafic, vous pouvez utiliser des machines virtuelles partagées. Pour les charges plus élevées, vous pouvez choisir des machines virtuelles dédiées de différentes tailles. Plusieurs applications App Service peuvent partager le même plan. Elles montent et descendent en puissance avec le plan.

Si vous avez besoin de plus d’évolutivité et d’isolement réseau, vous pouvez exécuter vos applications dans un [environnement App Service](../app-service-web/app-service-app-service-environment-intro.md).

## Tarification

Pour plus d’informations sur le coût d’App Service, consultez la page [Tarification de App Service](https://azure.microsoft.com/pricing/details/app-service/).

## Prise en main d'App Service

[Créez une application web, mobile ou logique temporaire](http://go.microsoft.com/fwlink/?LinkId=523751) immédiatement et gratuitement sans carte de crédit, sans aucun engagement et sans aucune contrainte.

Ou ouvrez un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/), puis essayez l’un de nos didacticiels dédiés à la prise en main :

* [Didacticiel : Créer une application web](../app-service-web/app-service-web-get-started.md)
* [Didacticiel : Créer une application mobile](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Didacticiel : Créer une application API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Didacticiel : Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->