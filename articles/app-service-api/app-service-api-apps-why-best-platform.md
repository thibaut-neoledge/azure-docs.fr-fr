<properties 
	pageTitle="Vue d'ensemble d'API Apps" 
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

# Vue d'ensemble d'API Apps

API Apps est l'un des quatre types d'application proposés par l'[Azure App Service](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Les applications API offrent une plateforme enrichie pour la création, l’hébergement et l’utilisation d’API dans le cloud et en local. Déployez votre API en tant qu'application API dans App Service et bénéficiez d'une sécurité de classe entreprise, d'un contrôle d'accès simple, d'une connectivité hybride, de la génération automatique du kit de développement logiciel et d'une intégration transparente dans [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Quel est le rôle d’API Apps ?

API Apps fournit les fonctionnalités suivantes disponibles aujourd'hui en version préliminaire publique :

- **Utilisation facile** : le support [Swagger](#concepts) intégré facilite l’utilisation de vos API par un certain nombre de clients. Configurez facilement [CORS](#concepts) et générez automatiquement le code client de vos API dans divers langages, par exemple C#, Java et Javascript.

- **Contrôle d'accès simple** : vous pouvez protéger une application API de tout accès non authentifié sans apporter de modification à votre code. Des services d'authentification intégrés sécurisent les API contre tout accès par d'autres services ou par des clients représentant des utilisateurs. Les fournisseurs d'identité pris en charge incluent Azure Active Directory et des fournisseurs tiers tels que Facebook et Twitter. Les clients peuvent utiliser l'Active Directory Authentication Library (ADAL) ou le kit de développement logiciel d'applications mobiles. Pour plus d’informations, consultez la page [Authentification pour les applications d’API et les applications mobiles dans Azure App Service](../app-service/app-service-authentication-overview.md).

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement, de consommation, de débogage et de gestion des applications API.

- **Intégration dans Logic Apps** : les applications API que vous créez peuvent être utilisées par [App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Insérez votre API existante en l'état** : vous n'avez pas besoin de modifier le code de vos API existantes pour bénéficier des fonctionnalités d'application API Apps. Il vous suffit de déployer votre code dans une application API. Votre API peut utiliser n'importe quel langage ou framework pris en charge par App Service, notamment ASP.NET et c#, Java, PHP, Node.js et Python.

Il ne s'agit que de quelques points clés Pour connaître plus de fonctionnalités qu'API Apps peut exploiter, consultez la [Vue d'ensemble de Web Apps](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE]Vous pouvez utiliser [Azure API Management](../api-management/api-management-key-concepts.md) pour contrôler l'accès par des clients à des API qui hébergées par App Service API Apps. Alors qu'API Apps fournit des services d'authentification, il existe d'autres fonctionnalités de gestion de l'accès proposées par API Management (et non par API Apps), telles que la consolidation du point de terminaison et la limitation.
>
>Les applications API sont actuellement en version préliminaire publique. [App Service Web Apps](../app-service-web/app-service-web-overview.md) est un service en disponibilité générale conçu pour la création et l'hébergement d'applications stratégiques sécurisées à l'échelle mondiale. Si vous recherchez un service en disponibilité générale pour créer votre API aujourd’hui, Web Apps est une option intéressante. Quand la fonction API Apps passera en disponibilité générale, nous proposerons un chemin de mise à jour des applications web existantes et d’exploitation des fonctionnalités supplémentaires proposées par API Apps.

## Concepts liés aux applications API ##

- **Swagger** : infrastructure de documentation interactive et de détection d'API RESTful, utilisée par défaut dans les applications API. Pour plus d’informations, consultez la page [http://swagger.io/](http://swagger.io/).
- **Cross Origin Resource Sharing (CORS)** : un mécanisme permettant à une instance de JavaScript exécutée dans un navigateur d'appeler une API hébergée sur un autre domaine que celui à partir duquel la page Web a été chargée.
- **Déclencheur** : API REST que les [applications logiques](../app-service-logic/app-service-logic-what-are-logic-apps.md) peuvent appeler pour lancer un processus de flux de travail lorsqu’une certaine condition est remplie. Par exemple, une application API peut fournir une méthode que l’application logique appelle régulièrement pour rechercher une certaine expression dans un flux Twitter. Pour plus d’informations, consultez la page [Déclencheurs des applications API](app-service-api-dotnet-triggers.md).
- **Action** : API REST que les [applications logiques](../app-service-logic/app-service-logic-what-are-logic-apps.md) peuvent appeler pour traiter les données après le démarrage d’un flux de travail par un déclencheur. Par exemple, l’application API peut fournir une méthode que l’application logique appelle pour répondre à un tweet trouvé par le déclencheur Twitter. Les actions sont des méthodes API exposées par une définition de l’API Swagger.
- **Passerelle** : application web qui gère les fonctions d’administration des API et l’authentification de toutes les applications API dans un groupe de ressources.

## Prise en main

Pour prendre en main les applications API, suivez le didacticiel [Créer une application API](app-service-dotnet-create-api-app.md).

Pour voir la liste des problèmes connus liés à API Apps, reportez-vous au [billet de forum sur les problèmes connus d'API Apps](https://social.msdn.microsoft.com/Forums/fr-FR/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Nov15_HO2-->