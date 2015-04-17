<properties 
	pageTitle="Que sont les applications API ?" 
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
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# Que sont les applications API ?

## Vue d'ensemble

Azure App Service est une plateforme de calcul entièrement gérée pour les développeurs professionnels qui offre un riche ensemble de fonctionnalités aux scénarios d'intégration, web et d'applications mobiles. Les applications API représentent une partie de la suite App Service et permettent à tout utilisateur technique ou développeur de découvrir, d'héberger, de gérer et de monétiser des connecteurs SaaS et d'API sur une plateforme du cloud moderne, riche en fonctionnalités, évolutive et globalement disponible.

## Quel est le rôle des applications API ?

Les applications API dans Azure App Service permettent de développer, publier, gérer et monétiser facilement des API.

- **Présentation de votre API en l'état** : utilisez ASP.NET, Java, PHP, Node.js ou Python pour vos API. Vos API peuvent tirer parti des fonctionnalités d'Azure App Service sans aucune modification.

- **Connexion de vos applications à des plateformes SaaS populaires** : Azure App Service facilite la connexion à des plateformes SaaS populaires, parmi lesquelles Salesforce, Office 365, Twitter, Facebook, Dropbox.

- **Contrôle d'accès simple** : les API peuvent être exposées uniquement aux autres applications dans Azure App Service ou publiquement, et vous pouvez ajouter une authentification par Azure Active Directory ou des services tiers sans modifier votre code.

- **Galerie publique et d'organisation des API** : partagez aisément les API avec d'autres équipes de votre organisation en utilisant votre propre galerie organisationnelle privée d'API. Les API peuvent également être partagées publiquement pour être utilisées par des développeurs tiers.

- **Génération automatique de Kits de développement logiciel (SDK)** : Azure App Service peut générer automatiquement des Kits de développement logiciel (SDK) pour plusieurs langages, notamment C#, Java et Javascript, ce qui met vos API à la disposition de nombreuses plateformes.

- **Utilisation du meilleur IDE du marché** : l'intégration [Visual
Studio](/campaigns/visual-studio-2013/) simplifie la création, le débogage, l'empaquetage et la publication des API, et permet de gérer le cycle de vie complet des applications.

<!--suppression selon la directive de réduire la taille de cette section
- **Aucune opération** : exécutez vos applications API dans un environnement haute disponibilité avec mise à jour corrective automatique. Les applications API déployées avec Azure App Service sont isolées et hébergées dans des machines virtuelles dédiées à vos applications, ce qui permet de garantir l'isolation de la sécurité et des performances prévisibles.

- **Mise à l'échelle automatique** : Azure App Service vous permet d'augmenter rapidement la taille des instances pour gérer n'importe quelle charge cliente entrante. Sélectionnez manuellement le nombre et la taille des machines virtuelles ou configurez la
[mise à l'échelle automatique](/documentation/videos/auto-scaling-azure-web-sites/)
pour adapter vos serveurs à la charge ou planification.
-->

- **Accès aux données locales** : les applications API peuvent utiliser des données de votre propre centre de données à l'aide de [connexions hybrides](integration-hybrid-connection-overview.md) et [réseaux virtuels](web-sites-integrate-with-vnet.md).

- **Déploiement sans friction** : configurez des workflows d'intégration et de déploiement continus avec Visual Studio Online, GitHub, TeamCity, Hudson ou BitBucket ; vous pouvez ainsi générer, tester et déployer automatiquement votre application API après chaque test d'intégration ou de vérification de code réussi.

## Concepts liés aux applications API ##

- **Galerie d'applications API** : effectuez votre choix parmi une liste toujours croissante de modèles d'application API existants.
- **Connecteurs** : type d'application API qui permet de se connecter facilement aux plateformes SaaS comme Salesforce et Office 365.
- **Passerelle** : application web qui gère les fonctions de gestion des API telles que l'authentification pour toutes les applications API dans un groupe de ressources. 
- **Mise à l'échelle automatique** : la taille des instances des applications API peut être augmentée automatiquement pour gérer n'importe quelle charge cliente entrante. Sélectionnez manuellement le nombre et la taille des machines virtuelles ou configurez la mise à l'échelle automatique pour adapter vos serveurs à la charge ou planification.
- **Intégration continue** : configurez des workflows d'intégration et de déploiement continus avec VSO, GitHub, TeamCity, Hudson ou BitBucket ; vous pouvez ainsi générer, tester et déployer automatiquement votre application web après chaque test d'intégration ou de vérification de code réussi.

## Prise en main

Pour prendre en main les applications API, suivez le [didacticiel Créer une application API](app-service-dotnet-create-api-app.md).

Pour plus d'informations sur la plateforme Azure App Service, consultez [Azure App Service](app-service-value-prop-what-is.md).

<!--HONumber=49-->