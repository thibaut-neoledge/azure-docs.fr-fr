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
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# Que sont les applications API ?

Les applications API font partie de la suite [Azure App Service](app-service-value-prop-what-is.md) qui comprend également les applications web, les applications mobiles et les applications logiques.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Les applications API de cette suite offrent une plateforme et un écosystème riches pour la création, l’utilisation et la distribution d’API dans le cloud et en local.

>[AZURE.NOTE]Les applications API sont actuellement en version préliminaire publique. Cette fonctionnalité se base sur [App Service Web Apps](app-service-web-overview.md), un service en disponibilité générale conçu pour la création et l’hébergement d’applications critiques sécurisées à l’échelle mondiale. Si vous recherchez un service en disponibilité générale pour créer votre API aujourd’hui, Web Apps est une option intéressante. Quand API Apps passera en disponibilité générale, nous proposerons un processus de mise à jour des applications web existantes pour qu’elles exploitent les fonctionnalités des applications API.

## Quel est le rôle d’API Apps ?

Les applications API sont des [applications web App Service](app-service-web-overview.md) avec des fonctionnalités supplémentaires qui améliorent l’expérience de développement, de déploiement, de publication, d’utilisation, de gestion et de valorisation des API web RESTful.

Ce qui veut dire que les applications API partagent avec les applications web toutes les fonctionnalités d’hébergement web de la plateforme Azure App Service :

- Application automatique des correctifs au système d’exploitation
- Sécurité de niveau entreprise
- Haute disponibilité
- Mise à l’échelle automatique et équilibrage de charge
- [WebJobs](websites-webjobs-resources.md) pour le traitement en arrière-plan
- Déploiement rapide et simple et nombreuses options de livraison continue. Pour plus d’informations sur les options de déploiement disponibles pour les applications API, consultez la page [Déployer une application web dans Azure App Service](web-sites-deploy.md). 

Les fonctionnalités supplémentaires fournies par la plateforme API Apps facilitent le développement, l’hébergement et l’utilisation des API :

- **Présentation de votre API en l’état** : utilisez ASP.NET, Java, PHP, Node.js ou Python pour vos API. Vos API peuvent tirer parti de la plateforme API Apps sans modifications.

- **Contrôle d’accès simple** : configurez vos API pour l’authentification par Azure Active Directory ou d’autres services comme Facebook et Twitter, sans modification de votre code. Utilisez une syntaxe simplifiée pour le code d’autorisation. Pour plus d’informations, consultez la page [Protéger une application API](app-service-api-dotnet-add-authentication.md).

- **Connexion facile aux plateformes SaaS** : les [applications API de connecteur](app-service-logic-what-are-biztalk-api-apps.md) dans Azure Marketplace sont fournies par Microsoft et certaines autres sociétés pour simplifier le code à écrire pour assurer l’interaction avec SalesForce, Office 365, Twitter, Facebook, Dropbox et bien d’autres encore.

- **Accès aux données locales** : API Apps peut exposer des API qui utilisent des données de votre propre centre de données à l’aide de [connexions hybrides](integration-hybrid-connection-overview.md) et de [réseaux virtuels](web-sites-integrate-with-vnet.md).

- **Utilisation facile** : utilisez [Swagger](http://swagger.io/) pour rendre votre API facilement utilisable par de nombreux clients. Ou bien utilisez le Kit de développement logiciel (SDK) API Apps pour générer le code client de votre API dans divers langages, par exemple c#, Java et Javascript.

- **Intégration aux applications logiques** : les applications API que vous créez peuvent être utilisées par les applications logiques App Service.

- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de [création](app-service-dotnet-create-api-app.md), de [déploiement](app-service-dotnet-deploy-api-app.md), de [débogage](app-service-dotnet-remotely-debug-api-app) et de gestion des applications API.

Dans un avenir proche, la plateforme API Apps créera également un écosystème complet d’API en simplifiant le partage de votre code :

- **Marchés publics et privés** : [Azure Marketplace](http://azure.microsoft.com/marketplace/) rend plus accessibles et plus simples à déployer sur votre abonnement Azure des applications API prépackagées développées par Microsoft et d’autres sociétés. Et vous serez en mesure de packager et de publier les applications API que vous développez, afin que les autres développeurs les déploient sur leur abonnement Azure. Lorsque vous publiez vos API sur Azure Marketplace, vous pourrez les rendre accessibles uniquement à d’autres membres de votre organisation. 

- **Déploiement automatique des dépendances** : chaque fois que vous déployez une application API depuis le Marketplace vers votre abonnement Azure, Azure déploie automatiquement les applications API dépendantes et crée les ressources nécessaires. Les packages d’application API spécifient les applications API dont ils dépendent, ainsi que les ressources Azure dont ils ont besoin.

- **Mises à jour automatiques** : lorsque vous mettez à jour le code d’un des packages d’application API que vous avez partagés, vous êtes en mesure de distribuer la mise à jour à tous les utilisateurs qui ont installé et exécuté votre application API. Ceci fonctionnera pour les modifications sans rupture et les utilisateurs qui ont choisi de recevoir les mises à jour.

La plupart de ces fonctionnalités, comme le Marketplace public et les mises à jour automatiques, sont déjà disponibles pour les applications API fournies par Microsoft.

## Concepts liés aux applications API ##

- **Passerelle** : application web qui gère les fonctions d’administration des API et l’authentification de toutes les applications API dans un groupe de ressources. 
- **Swagger** : infrastructure de documentation interactive et de détection d’API RESTful, utilisée par défaut dans les applications API. Pour plus d’informations, consultez la page [http://swagger.io/](http://swagger.io/).
- **Connecteur** : type d’application API qui permet de se connecter facilement aux plateformes SaaS comme Salesforce et Office 365. Pour plus d’informations, consultez la page [Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md).
- **Déclencheur** : API REST que les [applications logiques](app-service-logic-what-are-logic-apps.md) peuvent appeler pour lancer un processus de flux de travail lorsqu’une certaine condition est remplie. Par exemple, une application API peut fournir une méthode que l’application logique appelle régulièrement pour rechercher une certaine expression dans un flux Twitter. Pour plus d’informations, consultez la page [Déclencheurs des applications API](app-service-api-dotnet-triggers.md).
- **Action** : API REST que les [applications logiques](app-service-logic-what-are-logic-apps.md) peuvent appeler pour traiter les données après le démarrage d’un flux de travail par un déclencheur. Par exemple, l’application API peut fournir une méthode que l’application logique appelle pour répondre à un tweet trouvé par le déclencheur Twitter. Les actions sont des méthodes API exposées par une définition de l’API Swagger.

## Prise en main

Pour prendre en main les applications API, suivez le didacticiel [Créer une application API](app-service-dotnet-create-api-app.md).

Pour voir la liste des problèmes connus des applications API, reportez-vous à [ce billet de forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](app-service-value-prop-what-is.md).


<!--HONumber=54-->