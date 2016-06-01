<properties
	pageTitle="Présentation de Web Apps | Microsoft Azure"
	description="Découvrez comment Azure App Service vous aide à développer et héberger des applications web."
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/16/2016"
	ms.author="tdykstra"/>

# Vue d'ensemble de Web Apps

*App Service Web Apps* est une plateforme de calcul entièrement gérée, optimisée pour l’hébergement de sites et d’applications web. Cette offre [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (Plateforme en tant que service) de Microsoft Azure vous permet de donner la priorité à votre logique métier tandis qu’Azure gère l’infrastructure servant à exécuter et à faire évoluer vos applications.

Pour découvrir la plateforme en 5 minutes, visualisez la vidéo [Azure App Service Web Apps with Yochay Kiriaty](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/) (Azure App Service Web Apps avec Yochay Kiriaty).

## Qu’est-ce qu’une application web dans App Service ?

Dans App Service, une *application web* constitue les ressources de calcul fournies par Azure pour l’hébergement d’un site ou d’une application web.

Les ressources de calcul peuvent se trouver sur des machines virtuelles partagées ou dédiées, selon le niveau tarifaire que vous choisissez. Le code de votre application s’exécute sur une machine virtuelle isolée des autres clients.

Votre code peut être écrit dans n’importe quel langage ou infrastructure pris en charge par [Azure App Service](../app-service/app-service-value-prop-what-is.md), tels que ASP.NET, Node.js, Java, PHP ou Python. Vous pouvez également exécuter des scripts utilisant [PowerShell et d’autres langages de script](web-sites-create-web-jobs.md#acceptablefiles) dans une application web.

Pour obtenir des exemples de scénarios d’application standard pour lesquels Web Apps peut être utilisé, consultez les [Scénarios Web Apps](https://azure.microsoft.com/documentation/scenarios/web-app/).

## Pourquoi utiliser Web Apps ?

Voici les principales fonctionnalités de Web Apps :

- **Familier et rapide** : utilisez vos compétences pour écrire du code dans votre langage, votre infrastructure et votre environnement de développement intégré (IDE) de prédilection. Configurez de nouvelles applications web et déployez du code vers celles-ci en quelques secondes.

- **Professionnel** : Web Apps est conçu pour la création et l’hébergement d’applications stratégiques sécurisées. Créez des applications d’entreprise Active Directory intégrées qui se connectent en toute sécurité aux ressources locales, puis hébergez-les sur une plateforme cloud conforme aux normes [ISO](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [SOC](https://www.microsoft.com/TrustCenter/Compliance/SOC) et [PCI](https://www.microsoft.com/TrustCenter/Compliance/pci). Le tout fourni avec un [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/app-service/) professionnel.

- **Portée mondiale** : Web Apps permet de réaliser rapidement une montée [en puissance](../app-service/app-service-scale.md) ou une [montée en charge](../azure-portal/insights-how-to-scale.md) pour gérer la charge entrante des clients. Sélectionnez manuellement le nombre et la taille des machines virtuelles ou configurez la mise à l’échelle automatique en fonction de la charge ou la planification. L’infrastructure de centre de données mondiale de Microsoft héberge vos applications web et facilite la réplication des données et l’hébergement des services dans plusieurs zones.

- **Azure Marketplace** : sélectionnez parmi la [liste des modèles d’applications web](https://azure.microsoft.com/marketplace/) qui est alimentée en permanence. Profitez du meilleur de la communauté des applications OSS grâce à une installation en un clic de packages tels que Wordpress, Joomla et Drupal.

- **Intégration continue** : configurez l’[intégration et les flux de travail de déploiement continus](app-service-continous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Générez, testez et déployez votre application web de manière automatique après chaque test d’intégration du code réussi.

- **Environnements intermédiaires et de test** : mettez en place un [déploiement intermédiaire](web-sites-staged-publishing.md) pour vérifier votre code dans un environnement de pré-production identique à votre environnement de production. Quand vous êtes prêt, publiez une nouvelle version de votre application sans interruption de service en effectuant une opération d’échange.

- **Tests dans un environnement de production** : allez au-delà du déploiement intermédiaire [en effectuant des tests A/B](app-service-web-test-in-production-get-start.md) pour vérifier votre nouveau code avec une fraction configurable de votre trafic.

- **WebJobs** [: exécutez n’importe quel programme ou script](web-sites-create-web-jobs.md) sur des machines virtuelles Web Apps. Exécutez des tâches en continu, selon une planification, ou déclenchées par des événements. Le [Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-get-started.md) Azure simplifie le code que vous écrivez pour l’intégration à d’autres services Azure, tels que les files d’attente de stockage, les objets blob, les tables ainsi que les files d’attente et rubriques Service Bus.

- **Connexions hybrides** : accédez aux données locales à l’aide de [connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md) et de [réseaux virtuels](../app-service-web/web-sites-integrate-with-vnet.md).

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement, de consommation, de débogage et de gestion des applications web. Pour en savoir plus, consultez [Annonce du Kit de développement logiciel (SDK) Microsoft Azure version 2.8.1 pour .NET](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

En outre, une application web peut tirer parti des fonctionnalités offertes par [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (telles que la prise en charge de CORS) et [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (telles que les notifications Push). L’inverse est également vrai : vous pouvez utiliser une application API ou une application mobile pour héberger une application web et tirer parti des fonctionnalités de Web Apps, telles que la mise à l’échelle automatique et le déploiement intermédiaire. La seule différence entre les trois types d’application (API, web, mobiles) est qu’elles s’affichent avec un nom et une icône différents dans le portail Azure. Pour plus d’informations sur les types d’application dans App Service, consultez l’article [Qu’est-ce qu’Azure App Service ?](../app-service/app-service-value-prop-what-is.md)

## Prise en main

Pour démarrer en déployant un exemple de code vers une nouvelle application web dans App Service, suivez le didacticiel [Prise en main des applications web dans Azure App Service](app-service-web-get-started.md). Vous devrez créer un compte Azure gratuit

Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<!---HONumber=AcomDC_0525_2016-->