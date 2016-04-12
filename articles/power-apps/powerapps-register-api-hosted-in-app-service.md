<properties
	pageTitle="Développement ou création d’une API hébergée dans l’environnement App Service de PowerApps entreprise | Microsoft Azure"
	description="Découvrez comment inscrire une API personnalisée hébergée dans un environnement App Service dans le portail Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Inscription d’une API hébergée dans votre environnement App Service
PowerApps prend en charge l’inscription d’API existantes hébergées n’importe où dans le cloud ou sur site. Il s’agit d’une fonctionnalité puissante. Dans certains scénarios, il est plutôt recommandé de développer ou de créer de nouvelles API. Par exemple, vous pouvez :

- implémenter de nouvelles fonctionnalités dans votre organisation à utiliser ;
- exploiter des fonctionnalités ou données existantes pour fournir une meilleure expérience de création d’applications aux utilisateurs.

Lorsque vous hébergez vos API dans votre environnement App Service, vous tirez parti de toutes les fonctionnalités existantes de l’[environnement App Service](../app-service-web/app-service-app-service-environment-intro.md) et d’une expérience d’intégration optimisée.

Pour utiliser ces API dans vos applications, vous devez « enregistrer » les API dans le portail Azure à l'aide d'une API gérée, d’API existantes dans votre environnement App Service, ou en créant une API à l'aide de Swagger.

> [AZURE.SELECTOR]
- [API gérées](../articles/power-apps/powerapps-register-from-available-apis.md)
- [API dans votre ASE](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [API Swagger](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

Dans cette rubrique, nous nous concentrons sur la deuxième option : **Inscrire une application web, une application API et une application mobile hébergées dans votre environnement App Service**.

#### Conditions préalables

- Inscrivez-vous à [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Créez un [environnement App Service](powerapps-get-started-azure-portal.md).


## Développement et déploiement d’une API dans votre environnement App Service

Le développement d’une API dans l’environnement App Service est très simple. Vous choisissez votre langage de programmation préféré pour créer une API Web, puis utilisez [Swagger 2.0](http://swagger.io) pour décrire la définition d’API. Voici quelques exemples :

- [Créer et déployer une infrastructure .NET dans Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md)
- [Créer et déployer une application API Java dans Azure App Service](../app-service-api/app-service-api-java-api-app.md)
- [Créer et déployer une application API Node.js dans Azure App Service](../app-service-api/app-service-api-nodejs-api-app.md)

Vous pouvez également déployer votre API web dans un environnement App Service, notamment à partir de Visual Studio et en continu à l’aide d’un système de contrôle de code source. La documentation sur le [déploiement d’une application web dans Azure App Service](../app-service-web/web-sites-deploy.md) est une ressource pertinente.

## Inscription de votre API personnalisée dans l’environnement App Service

Une fois l’API déployée dans votre environnement App Service, procédez comme suit pour l’inscrire :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **PowerApps**, puis **Manage APIs (Gérer les API)** : ![][11]
2. Dans Gérer les API, sélectionnez **Ajouter** : ![][12]  
3. Dans **Ajouter une API**, entrez les propriétés de l’API :  

	- Dans **Nom**, entrez un nom pour votre API. Notez que le nom que vous entrez est inclus dans l’URL d’exécution de l’API. Utilisez un nom descriptif qui soit unique au sein de votre organisation.	
	- Dans **Source**, sélectionnez **Import from APIs hosted in App Service Environment (Importer à partir d’API hébergées dans un environnement App Service)** : ![][13]
4. Dans **API hosted in App Service Environment (API hébergée dans un environnement App Service)**, sélectionnez l’API que vous souhaitez importer. Cette liste répertorie chaque application web, API et mobile dans votre environnement App Service dont la propriété **apiDefinition.url** est configurée. La définition d’API Swagger 2.0 exposée à l’aide de cette propriété est utilisée pour importer l’API. Assurez-vous que cette URL est publiquement accessible lorsque vous inscrivez l’API : ![][14]
5. Sélectionnez **ADD (Ajouter)** pour terminer ces étapes.

> [AZURE.TIP] Quand vous inscrivez une API, vous l’inscrivez dans votre environnement App Service. Une fois dans l’environnement App Service, elle peut être utilisée par d’autres applications de l’environnement.

## Résumé et étapes suivantes
Cette rubrique vous a montré comment inscrire une API hébergée dans votre environnement App Service. Pour plus d’informations sur PowerApps, consultez les rubriques et ressources connexes suivantes :

- [Configurer les propriétés de l’API](powerapps-configure-apis.md)
- [Octroyer aux utilisateurs l’accès aux API](powerapps-manage-api-connection-user-access.md)
- [Créer des applications dans PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---------HONumber=AcomDC_0309_2016-->