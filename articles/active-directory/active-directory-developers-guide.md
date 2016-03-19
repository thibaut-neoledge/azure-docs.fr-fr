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
   ms.date="02/25/2016"
   ms.author="mbaldwin"/>


# Guide du développeur Azure Active Directory

## Vue d'ensemble
En tant que plateforme de gestion des identités en tant que service (IDMaaS), Azure Active Directory fournit aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les fonctionnalités clés d’Azure Active Directory. Nous vous suggérons de les lire dans l’ordre ou de passer à la rubrique [Prise en main](#getting-started) si vous êtes prêt à examiner les choses de façon approfondie.


1. [Avantages de l’intégration Azure Active Directory](active-directory-how-to-integrate.md) : découvrez pourquoi l’intégration à Active Directory Azure offre la meilleure solution pour bénéficier d’une connexion et d’une autorisation sécurisées.

1. [Scénarios d’authentification Active Directory](active-directory-authentication-scenarios.md) : tirez parti de l’authentification simplifiée d’Azure Active Directory pour fournir une connexion à votre application.

1. [Intégration d’applications à Azure Active Directory](active-directory-integrating-applications.md) : apprenez à ajouter, mettre à jour et supprimer des applications dans Azure Active Directory et découvrez les directives de personnalisation pour les applications intégrées.

1. [API Graph Azure Active Directory](active-directory-graph-api.md) : utilisez l’API Graph Azure Active Directory pour accéder par programmation à Azure Active Directory via des points de terminaison d’API REST. Notez que l’API Graph Azure AD est également accessible au moyen de [Microsoft Graph](https://graph.microsoft.io/), une API unifiée qui permet d’accéder à plusieurs API du service cloud Microsoft à travers un point de terminaison API REST unique et avec un jeton d’accès unique.

1. [Bibliothèques d’authentification d’Azure Active Directory](active-directory-authentication-libraries.md) : facilitez l’authentification des utilisateurs pour l’obtention de jetons d’accès à l’aide des bibliothèques d’authentification AD Azure pour .NET, JavaScript, Objective-C, Android et autres.


## Prise en main

Ces didacticiels sont adaptés pour plusieurs plateformes et vous aident à commencer rapidement le travail de développement avec Azure Active Directory. Vous devez préalablement [obtenir un client Azure Active Directory](active-directory-howto-tenant.md).

### Guides de démarrage rapide pour les applications mobiles et PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Guides de démarrage rapide pour les applications web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md)

### Guides de démarrage rapide pour les API web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.JS](active-directory-devquickstarts-webapi-nodejs.md)

### Interrogation du guide de démarrage rapide du répertoire

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API Graph](active-directory-graph-api-quickstart.md)|

## Procédures

Ces articles décrivent comment effectuer des tâches spécifiques avec Azure Active Directory :

- [Obtention d’un client Azure Active Directory](active-directory-howto-tenant.md)
- [Affichage de votre application dans la galerie d’applications Azure Active Directory](active-directory-app-gallery-listing.md)
- [Présentation du manifeste d’application Azure Active Directory](active-directory-application-manifest.md)
- [Créer une application avec les API Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Envoyer des applications web pour Office 365 à Mon tableau de bord vendeur](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Version préliminaire : comment créer des applications qui connectent les utilisateurs avec à la fois des comptes personnels et des comptes professionnels ou scolaires](active-directory-appmodel-v2-overview.md)
- [Version préliminaire : création d’applications qui inscrivent et connectent les consommateurs](../active-directory-b2c/active-directory-b2c-overview.md)


## Référence

Ces articles fournissent une référence de base pour les API REST et de bibliothèque d’authentification, les protocoles, les erreurs, les exemples de code et les points de terminaison.

###  Support
- [Questions balisées](http://stackoverflow.com/questions/tagged/azure-active-directory) : recherchez des solutions Azure Active Directory en cas de dépassement de la capacité de la pile en recherchant les balises [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).

### Code

- [Bibliothèques open source Azure Active Directory](http://github.com/AzureAD) : le moyen le plus simple pour rechercher la source d’une bibliothèque consiste à utiliser notre [liste de bibliothèques](active-directory-authentication-libraries.md).

- [Exemples Azure Active Directory](https://github.com/azure-samples?query=active-directory) : le moyen le plus simple de parcourir la liste d’exemples est d’utiliser l’[index des exemples de code](active-directory-code-samples.md).


### API Graph

- [Informations de référence sur l’API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx) : informations de référence REST pour l’API Azure Active Directory Graph. [Découvrez l’expérience interactive de référence de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Étendues d’autorisation de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) : étendues d’autorisation OAuth 2.0 utilisées pour contrôler l’accès d’une application aux données d’annuaire d’un client.

### Bibliothèques d’authentification

- [.NET](https://msdn.microsoft.com/library/azure/mt417579.aspx) : documentation de la bibliothèque d’authentification .NET.

### Protocoles d’authentification

- [Référence du protocole SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx) : le protocole SAML 2.0 permet aux applications de fournir une expérience d’authentification unique à leurs utilisateurs.


- [Informations de référence sur le protocole OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) : vous pouvez utiliser le protocole OAuth 2.0 pour autoriser l’accès aux applications web et aux API web dans votre client Azure Active Directory.


- [Référence du protocole OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) : le protocole OpenID Connect 1.0 étend OAuth 2.0 pour une utilisation comme protocole d'authentification.


- [Informations de référence sur le protocole WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) : le protocole WS-Federation 1.2 est spécifié dans la spécification Web Services Federation Version 1.2.

- [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md) : vous pouvez utiliser ce guide pour comprendre et évaluer les revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT).

## Vidéos

### Build 2015

Ces présentations sur le développement d'applications à l'aide d'Azure Active Directory mettent en scène des intervenants qui travaillent directement dans l'équipe d'ingénierie. Les présentations couvrent les sujets fondamentaux, notamment IDMaaS, l'authentification, la fédération d'identités et l'authentification unique.

- [Azure Active Directory : gestion des identités en tant que service pour les applications modernes](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Développement d’applications web modernes avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Développement d’applications web natives avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) est une série hebdomadaire de vidéos au format 1:1 qui vous propose de courts entretiens (10 à 15 minutes) avec des experts sur divers sujets relatifs à Azure. Utilisez la fonctionnalité Filtre des services sur la page pour afficher toutes les vidéos Azure Active Directory.

- [Identité Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identité Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identité Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Réseaux sociaux

- [Blog de l’équipe Active Directory](http://blogs.technet.com/b/ad/) : les derniers développements dans l’univers d’Azure Active Directory.

- [Blog de l’équipe Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam) : informations Azure Active Directory spécifiques à l’API Graph.

- [Cloud Identity](http://www.cloudidentity.net) : réflexions sur la gestion des identités en tant que service, par un directeur de programme Azure Active Directory.

- [Azure Active Directory sur Twitter](https://twitter.com/azuread) : annonces Azure Active Directory de moins de 140 caractères.

<!---HONumber=AcomDC_0302_2016-->