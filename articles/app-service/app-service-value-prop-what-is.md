<properties 
	pageTitle="Présentation d’Azure App Service | Microsoft Azure" 
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

*App Service* est une solution [platform-as-a-service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure, qui permet de créer des applications web et mobiles pour n’importe quelle plateforme ou appareil. Vous pouvez facilement intégrer des solutions SaaS à vos applications (par exemple, Office 365, Dynamics CRM, Salesforce, Twilio), vous connecter facilement à des applications locales (telles que SAP, Oracle, Siebel) et automatiser facilement des processus d’entreprise tout en répondant à des besoins stricts en termes de sécurité, fiabilité et évolutivité.

App Service inclut les fonctionnalités web et mobiles que nous avons précédemment fournies séparément en tant que sites web Azure et services mobiles Azure. Il inclut également de nouvelles fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud.

## Types d’applications dans App Service

App Service offre les types d’applications suivants pour l’exécution de votre code d’application ou de vos processus de flux de travail.

- [**Web Apps**](../app-service-web/app-service-web-overview.md) : pour l’hébergement de sites et d’applications web.

- [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) : pour l’hébergement de serveurs principaux d’application mobile.
   
- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) : pour l’hébergement des API cloud.
 
- [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) : pour automatiser l’accès et l’utilisation des données entre des clouds sans écrire de code.

En tant que service intégré unique, App Service facilite la composition de plusieurs types d’applications dans une solution unique.

## Environnements et plans App Service

[Plans App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) représente les ressources de calcul sur lesquelles vos applications s’exécutent. À des niveaux tarifaires inférieurs, vos applications s’exécutent sur des machines virtuelles partagées. À des niveaux plus élevés, vos applications s’exécutent sur des machines virtuelles dédiées. Vous avez le choix entre plusieurs tailles de machines virtuelles, et pouvez modifier votre niveau tarifaire sans interruption de service. Si vous avez besoin de plus d’évolutivité et d’isolement réseau, vous pouvez exécuter vos applications dans un [environnement App Service](../app-service-web/app-service-app-service-environment-intro.md).

## Pourquoi utiliser App Service ?

Voici quelques fonctionnalités et capacités clés d’App Service :

- **Plateforme entièrement gérée** : mise à jour corrective automatique de l’infrastructure et du système d’exploitation, prise en charge intégrée pour la sauvegarde et récupération d’urgence. 

- **Utilisation de vos compétences existantes** :écrivez du code dans votre langage, votre infrastructure et votre environnement de développement. App Service prend en charge .NET, Node.js, Java, PHP et Python.

- **Déploiement rapide** : approvisionnez de nouvelles applications et déployez du code vers celles-ci en quelques secondes.

- **Intégration continue** : configurez l’[intégration et le déploiement continus](../app-service-web/app-service-continous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket.

- **Environnements intermédiaires et de test** : mettez en place un [déploiement intermédiaire](../app-service-web/web-sites-staged-publishing.md) pour vérifier votre code dans un environnement de préproduction identique à votre environnement de production. Quand vous êtes prêt, publiez une nouvelle version de votre application sans interruption de service en effectuant une opération d’échange.

- **Tests dans un environnement de production** : allez au-delà du déploiement intermédiaire [en effectuant des tests A/B](../app-service-web/app-service-web-test-in-production-get-start.md) pour vérifier votre nouveau code avec une fraction configurable de votre trafic.

- **Authentification et autorisation** : protégez une application API de tout accès non authentifié sans apporter de modifications à votre code. Des services d’authentification intégrés sécurisent vos applications contre tout accès par des utilisateurs, des clients représentant des utilisateurs ou des services. Les fournisseurs d’identité pris en charge incluent Azure Active Directory, Facebook, Twitter, Google et Microsoft Account. Pour plus d’informations, consultez la page [Authentification et autorisation dans Azure App Service](app-service-authentication-overview.md).

- **Connexion à n’importe quel service** : connectez votre application aux systèmes d’entreprise ou aux plateformes SaaS en quelques minutes à l’aide de [connecteurs](../connectors/apis-list.md) intégrés. Choisissez parmi plus de 50 connecteurs pour des systèmes d’entreprise tels que SAP, Siebel et Oracle, des services SaaS d’entreprise populaires tels que Salesforce et Office 365, et des services Internet populaires tels que Facebook, Twitter et Dropbox.

- **Portée mondiale** : effectuez une montée [en puissance](../app-service/app-service-scale.md) ou une [montée en charge](../azure-portal/insights-how-to-scale.md) pour gérer la charge entrante des clients. Sélectionnez manuellement le nombre et la taille des machines virtuelles ou configurez la mise à l’échelle automatique en fonction de la charge ou la planification. L’infrastructure de centre de données mondiale de Microsoft héberge vos applications et facilite la réplication des données et l’hébergement des services dans plusieurs zones.

- **Professionnel** : App Service est conçu pour la création et l’hébergement d’applications stratégiques sécurisées. Créez des applications d’entreprise Active Directory intégrées qui se connectent en toute sécurité aux ressources locales, puis hébergez-les sur une plateforme cloud sécurisée conforme aux normes [ISO, SOC et PCI](https://www.microsoft.com/TrustCenter/). Le tout fourni avec un [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/app-service/) professionnel.

- **Azure Marketplace** : sélectionnez parmi la [liste des modèles d’applications](https://azure.microsoft.com/marketplace/) qui est alimentée en permanence. Profitez du meilleur de la communauté des applications OSS grâce à une installation en un clic de packages tels que Wordpress, Joomla et Drupal.

- **WebJobs** : [ exécutez n’importe quel programme ou script](../app-service-web/web-sites-create-web-jobs.md) sur des machines virtuelles App Service. Exécutez des tâches en continu, selon une planification, ou déclenchées par des événements. Le [Kit de développement logiciel (SDK) WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) Azure simplifie le code que vous écrivez pour l’intégration à d’autres services Azure et tiers.

- **Connexions hybrides** : accédez aux données locales à l’aide de [connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md) et de [réseaux virtuels Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement, de consommation, de débogage et de gestion des applications web, mobiles et API.

## Prise en main d'App Service

[Créez une application web, mobile ou logique temporaire](http://go.microsoft.com/fwlink/?LinkId=523751) immédiatement et gratuitement sans carte de crédit, sans aucun engagement et sans aucune contrainte.

Ou ouvrez un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/), puis essayez l’un de nos didacticiels dédiés à la prise en main :

* [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)
* [Mobile Apps](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [API Apps](https://azure.microsoft.com/documentation/services/app-service/api/)
* [Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->