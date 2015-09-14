<properties
   pageTitle="Guide du développeur Azure Active Directory | Microsoft Azure"
	description="Cet article fournit un guide complet sur les ressources destinées aux développeurs pour Azure Active Directory."
	services="active-directory"
	documentationCenter="dev-center-name"
	authors="msmbaldwin"
	manager="mbaldwin"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/12/2015"
	ms.author="mbaldwin"/>


# Guide du développeur Azure Active Directory

## Vue d'ensemble
En tant que plateforme de gestion des identités en tant que service (IDMaaS), Azure Active Directory fournit aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les fonctionnalités clés d’Azure Active Directory. Nous vous suggérons de les lire dans l’ordre ou de passer à la rubrique [Prise en main](#getting-started) si vous êtes prêt à examiner les choses de façon approfondie.


1. [Intégration avec Azure Active Directory](active-directory-how-to-integrate.md) : découvrez pourquoi l’intégration avec Active Directory Azure offre la meilleure solution pour une connexion et une autorisation sécurisées.

1. [Scénarios d’authentification Active Directory](active-directory-authentication-scenarios.md) : tirez parti de l’authentification simplifiée d’Azure Active Directory pour fournir une connexion à votre application.

1. [API Graph Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974476.aspx): utilisez l’API Graph Azure Active Directory pour accéder par programme à Azure Active Directory via les points de terminaison d’API REST.

1. [Intégration d’applications à Azure Active Directory](active-directory-integrating-applications.md) : apprenez-en plus sur l’inscription de votre application et les instructions de personnalisation pour les applications mutualisées.

1. [Bibliothèques d’authentification Azure Active Directory](active-directory-authentication-libraries.md) : facilitez l’authentification des utilisateurs pour l’obtention de jetons d’accès avec les bibliothèques d’authentification Azure.

Pour afficher les vues d’ensemble Azure Active Directory présentées à la conférence Build 2015, consultez la section [Vidéos](#videos) ci-dessous.


## Prise en main

Ces didacticiels sont adaptés pour plusieurs plateformes et vous aident à commencer rapidement le travail de développement avec Azure Active Directory. Vous devez préalablement [obtenir un client Azure Active Directory](active-directory-howto-tenant.md).

### Guides de démarrage rapide pour les applications mobiles et PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)


### Guides de démarrage rapide des applications web et des API web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|:--:|:--:
|[Application web .NET](active-directory-devquickstarts-webapp-dotnet.md)|[API web .NET](active-directory-devquickstarts-webapi-dotnet.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.JS](active-directory-devquickstarts-webapi-nodejs.md)

### Interrogation du guide de démarrage rapide du répertoire

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API Graph](active-directory-graph-api-quickstart.md)|

## Procédures

Ces articles décrivent comment effectuer des tâches spécifiques avec Azure Active Directory :

- [Obtention d’un client Azure Active Directory](active-directory-howto-tenant.md)
- [Affichage de votre application dans la galerie d’applications Azure AD](active-directory-app-gallery-listing.md)
- [Créer une application avec les API Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Envoyer des applications web pour Office 365 à Mon tableau de bord vendeur](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Version préliminaire : comment créer des applications qui connectent les utilisateurs avec à la fois des comptes personnels et des comptes professionnels ou scolaires](active-directory-appmodel-v2-overview.md)


## Référence

Ces articles fournissent une référence de base pour les API REST et de bibliothèque d’authentification, les protocoles, les erreurs, les exemples de code et les points de terminaison.

###  Support
- [Questions marquées](http://stackoverflow.com/questions/tagged/azure-active-directory) : trouvez des solutions Azure Active Directory en cas de dépassement de capacité de la pile en recherchant les balises [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).

### Code

- [Bibliothèques open source Azure Active Directory](http://github.com/AzureAD) : le moyen le plus simple pour rechercher la source d’une bibliothèque consiste à utiliser notre [liste de bibliothèques](active-directory-authentication-libraries.md).

- [Exemples Azure Active Directory](http://github.com/AzureADSamples) : le moyen le plus simple pour parcourir la liste d’exemples consiste à utiliser l’[Index des exemples de code](active-directory-code-samples.md).


### API Graph

- [Référence de l’API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx) : référence REST pour l’API Azure Active Directory Graph. [Découvrez l’expérience interactive de référence de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Étendues d’autorisation de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes) : étendues d’autorisation OAuth 2.0 qui sont utilisées pour contrôler l’accès aux données du répertoire dans un client.


### Protocoles d’authentification

- [Référence du protocole SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx) : le protocole SAML 2.0 permet aux applications de fournir une expérience d’authentification unique à leurs utilisateurs.


- [Référence du protocole OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) : vous pouvez utiliser le protocole OAuth 2.0 pour autoriser l’accès aux applications web et aux API web dans votre client Azure Active Directory.


- [Référence du protocole OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) : le protocole OpenID Connect 1.0 étend OAuth 2.0 pour une utilisation comme protocole d’authentification.


- [Référence du protocole WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) : le protocole WS-Federation 1.2 est spécifié dans la spécification Web Services Federation Version 1.2.

- [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md) : vous pouvez utiliser ce guide pour comprendre et évaluer les revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT).

## Vidéos

### Build 2015

Ces présentations sur le développement d'applications à l'aide d'Azure Active Directory mettent en scène des intervenants qui travaillent directement dans l'équipe d'ingénierie. Les présentations couvrent les sujets fondamentaux, notamment IDMaaS, l'authentification, la fédération d'identités et l'authentification unique.

- [Azure Active Directory : gestion des identités en tant que service pour les applications modernes](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [Développement d’applications web modernes avec Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [Développement d’applications web natives avec Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) est une série hebdomadaire de vidéos qui vous proposent de courts entretiens (10 à 15 minutes) avec des experts sur divers sujets relatifs à Azure. Utilisez la fonctionnalité Filtre des services sur la page pour afficher toutes les vidéos Azure Active Directory.

- [Identité Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identité Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identité Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Réseaux sociaux

- [Blog de l’équipe Active Directory](http://blogs.technet.com/b/ad/) : les derniers développements dans le monde d’Azure Active Directory.

- [Blog de l'équipe Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informations Azure Active Directory spécifiques à l'API Graph.

- [Cloud Identity](http://www.cloudidentity.net) : réflexions sur la gestion des identités en tant que service, par un directeur de programme Azure Active Directory.

- [Azure Active Directory sur Twitter](https://twitter.com/azuread): annonces Azure Active Directory de moins de 140 caractères.

<!---HONumber=September15_HO1-->