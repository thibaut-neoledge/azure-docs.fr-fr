<properties 
	pageTitle="Vue d’ensemble d’API Apps | Microsoft Azure" 
	description="Découvrez pourquoi Azure App Service est la meilleure plateforme de développement, de publication et d'hébergement des API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2016" 
	ms.author="tdykstra"/>

# Vue d'ensemble d'API Apps

API Apps est l'un des quatre types d'application proposés par l'[Azure App Service](../app-service/app-service-value-prop-what-is.md).

![Types d’applications dans Azure App Service](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

[App Service](../app-service/app-service-value-prop-what-is.md) est une plateforme entièrement gérée conçues pour les scénarios Web et mobiles ainsi que pour les scénarios d’intégration. API Apps dans App Service fournissent des fonctionnalités qui facilitent la gestion, l'hébergement et l'utilisation des API dans le cloud et en local. Avec API Apps, vous bénéficiez d'une sécurité de classe entreprise, d'un contrôle d'accès simple, d'une connectivité hybride, de la génération automatique du kit de développement logiciel (SDK) et d'une intégration transparente dans [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Quel est le rôle d’API Apps ?

Voici les principales fonctionnalités d'API Apps :

- **Utilisation facile** : le support intégré des [métadonnées d'API Swagger](#concepts) facilite l'utilisation de vos API par un certain nombre de clients. Générez automatiquement le code client de vos API dans divers langages, par exemple C#, Java et Javascript. Configurez facilement [CORS](#concepts) sans modifier votre code. Pour plus d'informations, consultez [Les métadonnées d'App Service API Apps pour la détection d'API et la création de code](app-service-api-metadata.md) et [Consommer une application API à partir de JavaScript à l'aide de CORS](app-service-api-cors-consume-javascript.md). 

- **Contrôle d'accès simple** : protégez une application API de tout accès non authentifié sans apporter de modification à votre code. Des services d'authentification intégrés sécurisent les API contre tout accès par d'autres services ou par des clients représentant des utilisateurs. Les fournisseurs d’identité pris en charge incluent Azure Active Directory, Facebook, Twitter, Google et Microsoft Account. Les clients peuvent utiliser l'Active Directory Authentication Library (ADAL) ou le kit de développement logiciel d'applications mobiles. Pour plus d’informations, consultez la page [Authentification et autorisation pour API Apps dans Azure App Service](app-service-api-authentication.md).

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement, de consommation, de débogage et de gestion des applications API. Pour en savoir plus, consultez [Annonce du Kit de développement logiciel (SDK) Microsoft Azure version 2.8.1 pour .NET](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Intégration dans Logic Apps** : les applications API que vous créez peuvent être utilisées par [App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md). Pour plus d’informations, consultez [Utilisation de votre API personnalisée hébergée sur App Service avec Logic Apps](../app-service-logic/app-service-logic-custom-hosted-api.md) et [Nouvelle version de schéma 2015-08-01-preview](../app-service-logic/app-service-logic-schema-2015-08-01.md).

- **Insérez votre API existante en l’état** : vous n’avez pas besoin de modifier le code de vos API existantes pour bénéficier des fonctionnalités d’application API Apps. Il vous suffit de déployer votre code dans une application API. Votre API peut utiliser n’importe quel langage ou framework pris en charge par App Service, notamment ASP.NET, C#, Java, PHP, Node.js et Python.

Une application API peut également tirer parti des fonctionnalités offertes par [Web Apps](../app-service-web/app-service-web-overview.md) et [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md). L’inverse est également vrai : si vous utilisez une application web ou une application mobile pour héberger une API, elle peut exploiter des fonctionnalités d’API Apps, telles que les métadonnées Swagger pour la génération de code client et CORS pour l’accès entre domaines via un navigateur. La seule différence entre les trois types d’applications (API, web, mobiles) est qu’elles s’affichent avec un nom et une icône différents dans le portail Azure.

## Amélioration d’API Apps à l’aide du service Gestion des API Azure 

API Apps et [Gestion des API Azure](../api-management/api-management-key-concepts.md) sont des services complémentaires :

* Le service Gestion des API est conçu pour gérer les API. Vous pouvez placer un composant frontal de Gestion des API sur une API pour surveiller et limiter l’utilisation, manipuler l’entrée et la sortie, unifier plusieurs API, etc. Les API gérées peuvent être hébergées n’importe où.
* Le service API Apps est conçu pour l’hébergement des API. Il comprend des fonctionnalités qui facilitent le développement et l’utilisation des API, mais à la différence du service Gestion des API, il ne permet pas de surveiller, de limiter, de manipuler ou d’unifier les API. 

Vous pouvez utiliser Gestion des API pour gérer les API hébergées par API Apps ou utiliser API Apps sans Gestion des API.

Certaines fonctionnalités de Gestion des API et API Apps présentent des fonctions similaires. Par exemple, les deux services permettent d’automatiser la prise en charge de CORS. Si vous les utilisez ensemble, vous devez utiliser Gestion des API pour CORS, car il joue le rôle de composant frontal de vos applications API.

## <a id="concepts"></a> Concepts liés à API Apps

- **Swagger** : infrastructure de documentation et de détection d’API RESTful, utilisée par défaut dans API Apps. Pour plus d’informations, consultez la page [http://swagger.io/](http://swagger.io/).
- **Cross Origin Resource Sharing (CORS)** : mécanisme permettant à une instance de JavaScript exécutée dans un navigateur d’appeler une API hébergée sur un autre domaine que celui à partir duquel la page web a été chargée. Pour plus d’informations, consultez [Consommer une application API à partir de JavaScript à l’aide de CORS](app-service-api-cors-consume-javascript.md). 
- **Déclencheur** : API REST que les [applications logiques](../app-service-logic/app-service-logic-what-are-logic-apps.md) peuvent appeler pour lancer un processus de flux de travail lorsqu’une certaine condition est remplie. Par exemple, une application API peut fournir une méthode que l’application logique appelle régulièrement pour rechercher une certaine expression dans un flux Twitter. Pour plus d’informations, consultez la page [Déclencheurs des applications API](app-service-api-dotnet-triggers.md).
- **Action** : API REST que les [applications logiques](../app-service-logic/app-service-logic-what-are-logic-apps.md) peuvent appeler pour traiter les données après le démarrage d’un flux de travail par un déclencheur. Par exemple, l’application API peut fournir une méthode que l’application logique appelle pour répondre à un tweet trouvé par le déclencheur Twitter. Les actions sont des méthodes API exposées par une définition de l’API Swagger.

## Prise en main

Pour prendre en main API Apps, suivez l’un des didacticiels [Prise en main d’API Apps](app-service-api-dotnet-get-started.md).

Pour poser des questions sur les applications API, démarrez un fil de discussion dans le [forum API Apps](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureAPIApps).

<!---HONumber=AcomDC_0413_2016-->