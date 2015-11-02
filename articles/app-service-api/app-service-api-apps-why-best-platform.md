<properties 
	pageTitle="Que sont les applications API ?" 
	description="Découvrez pourquoi Azure App Service est la meilleure plateforme de développement, de publication et d'hébergement des API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# Que sont les applications API ?

Les applications API offrent une plateforme enrichie pour la création, l’hébergement et l’utilisation d’API dans le cloud et en local. Déployez votre API sous forme d'application API et bénéficiez d’une sécurité de classe entreprise, d’un contrôle d'accès simple, d’une connectivité hybrides et SaaS, de la génération automatique de Kit de développement logiciel et d’une intégration transparente avec [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

Les applications API font partie de [Azure App Service](../app-service/app-service-value-prop-what-is.md) qui comprend également Web Apps, Mobile Apps et Logic Apps.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## Quel est le rôle d’API Apps ?

API Apps offre des fonctionnalités de développement, de déploiement, de publication, d’utilisation et de gestion des API web RESTful. App Service offre les fonctionnalités suivantes disponibles aujourd'hui en version préliminaire publique :

- **Utilisation facile** : le support [Swagger](http://swagger.io/) intégré facilite l’utilisation de vos API par un certain nombre de clients. Le Kit de développement logiciel (SDK) d’API Apps peut générer le code client de vos API dans divers langages, par exemple C#, Java et Javascript.

- **Contrôle d’accès simple** : les services d’authentification intégrés prennent en charge Azure Active Directory ou d’autres services tiers (comme Facebook et Twitter). Vous pouvez protéger une application API de tout accès non authentifié sans apporter de modifications à votre code. Si vous êtes familiarisé avec les services d’authentification fournis par [Azure Mobile Services](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication) : les applications API s’appuient sur cette infrastructure et s’étendent aux API hébergées par API Apps. Le Kit de développement logiciel (SDK) d’App Service vous permet également d’utiliser une syntaxe simplifiée pour le code d’autorisation. Pour plus d’informations, consultez la page [Authentification pour les applications d’API et les applications mobiles dans Azure App Service](../app-service/app-service-authentication-overview.md).

- **Connexion facile aux plateformes SaaS** : les [applications API de connecteur](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) dans Azure Marketplace sont fournies par Microsoft et certaines autres sociétés pour simplifier le code à écrire pour assurer l’interaction avec SalesForce, Office 365, Twitter, Facebook, Dropbox et bien d’autres encore.

- **Intégration dans Logic Apps** : les applications API que vous créez peuvent être utilisées par [App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de [création](app-service-dotnet-create-api-app.md), de [déploiement](app-service-dotnet-deploy-api-app.md), de [débogage](app-service-dotnet-remotely-debug-api-app) et de gestion des applications API.

Vous pouvez insérer votre API existante en l’état ; vous n’avez pas besoin de modifier le code de vos API existantes pour bénéficier des fonctions d’application API : il vous suffit de déployer votre code sur une application API. Vous pouvez utiliser ASP.NET, Java, PHP, Node.js ou Python pour vos API.

Applications API inclut en outre les [fonctions d’App Service Web Apps](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE] [Azure API Management](/services/api-management/) est un service distinct qui offre des fonctionnalités telles que la consolidation du point de terminaison et la limitation de requêtes. Vous pouvez utiliser l’API de gestion avec les applications API.
>
>Les applications API sont actuellement en version préliminaire publique. [App Service Web Apps](../app-service-web/app-service-web-overview.md) est un service en disponibilité générale conçu pour la création et l’hébergement d’applications critiques sécurisées à l’échelle mondiale. Si vous recherchez un service en disponibilité générale pour créer votre API aujourd’hui, Web Apps est une option intéressante. Quand la fonction API Apps passera en disponibilité générale, nous proposerons un chemin de mise à jour des applications web existantes et d’exploitation des fonctionnalités supplémentaires proposées par API Apps.

## Concepts liés aux applications API ##

- **Passerelle** : application web qui gère les fonctions d’administration des API et l’authentification de toutes les applications API dans un groupe de ressources. 
- **Swagger** : infrastructure de documentation interactive et de détection d’API RESTful, utilisée par défaut dans les applications API. Pour plus d’informations, consultez la page [http://swagger.io/](http://swagger.io/).
- **Connecteur** : type d’application API qui permet de se connecter facilement aux plateformes SaaS comme Salesforce et Office 365. Pour plus d’informations, consultez la page [Qu’est-ce qu’un connecteur et une application API BizTalk](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md).
- **Déclencheur** : API REST que les [applications logiques](../app-service-logic/app-service-logic-what-are-logic-apps.md) peuvent appeler pour lancer un processus de flux de travail lorsqu’une certaine condition est remplie. Par exemple, une application API peut fournir une méthode que l’application logique appelle régulièrement pour rechercher une certaine expression dans un flux Twitter. Pour plus d’informations, consultez la page [Déclencheurs des applications API](app-service-api-dotnet-triggers.md).
- **Action** : API REST que les [applications logiques](../app-service-logic/app-service-logic-what-are-logic-apps.md) peuvent appeler pour traiter les données après le démarrage d’un flux de travail par un déclencheur. Par exemple, l’application API peut fournir une méthode que l’application logique appelle pour répondre à un tweet trouvé par le déclencheur Twitter. Les actions sont des méthodes API exposées par une définition de l’API Swagger.

## Prise en main

Pour prendre en main les applications API, suivez le didacticiel [Créer une application API](app-service-dotnet-create-api-app.md).

Pour voir la liste des problèmes connus des applications API, reportez-vous à [ce billet de forum MSDN](https://social.msdn.microsoft.com/Forums/fr-FR/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Oct15_HO4-->