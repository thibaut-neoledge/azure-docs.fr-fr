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
   ms.date="07/06/2016"
   ms.author="mbaldwin"/>


# Guide du développeur Azure Active Directory

## Vue d'ensemble
En tant que plateforme de gestion des identités en tant que service (IDMaaS), Azure Active Directory (AD) fournit aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les fonctionnalités clés d’Azure AD. Nous vous suggérons de les lire dans l’ordre ou de passer à la rubrique [Prise en main](#getting-started) si vous êtes prêt à examiner les choses de façon approfondie.


1. [Avantages de l’intégration avec Azure Active Directory](active-directory-how-to-integrate.md) : découvrez pourquoi l’intégration avec Azure AD offre la meilleure solution pour une connexion et une autorisation sécurisées.

1. [Scénarios d’authentification Active Directory](active-directory-authentication-scenarios.md) : tirez parti de l’authentification simplifiée d’Azure AD pour fournir une connexion à votre application.

1. [Intégration d’applications à Azure Active Directory](active-directory-integrating-applications.md) : apprenez à ajouter, mettre à jour et supprimer des applications dans Azure AD et découvrez les directives de personnalisation pour les applications intégrées.

1. [API Graph Azure Active Directory](active-directory-graph-api.md) : utilisez l’API Graph Azure AD pour accéder par programme à Azure AD via les points de terminaison d’API REST. Notez que l’API Graph Azure AD est également accessible au moyen de [Microsoft Graph](https://graph.microsoft.io/), une API unifiée qui permet d’accéder à plusieurs API du service cloud Microsoft à travers un point de terminaison API REST unique et avec un jeton d’accès unique.

1. [Bibliothèques d’authentification d’Azure Active Directory](active-directory-authentication-libraries.md) : facilitez l’authentification des utilisateurs pour l’obtention de jetons d’accès à l’aide des bibliothèques d’authentification AD Azure pour .NET, JavaScript, Objective-C, Android et autres.


## Prise en main

Ces didacticiels sont adaptés pour plusieurs plateformes et vous aident à commencer rapidement le travail de développement avec Azure Active Directory. Vous devez préalablement [obtenir un client Azure Active Directory](active-directory-howto-tenant.md).

### Guides de démarrage rapide pour les applications mobiles et PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Universal](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Intégrer directement à OAuth 2.0](active-directory-protocols-oauth-code.md)|

### Guides de démarrage rapide pour les applications web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md)|[Intégrer directement à OpenID Connect](active-directory-protocols-openid-connect-code.md)|

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

- [Obtention d’un locataire Azure AD](active-directory-howto-tenant.md)
- [Connecter un utilisateur Azure AD à l’aide du modèle d’application mutualisée](active-directory-devhowto-multi-tenant-overview.md)
- Activer l’authentification unique entre applications en utilisant ADAL, sur [Android](active-directory-sso-android.md) et sur les appareils [iOS](active-directory-sso-ios.md)
- [Certifier votre application AppSource pour Azure AD](active-directory-devhowto-appsource-certified.md)
- [Afficher votre application dans la galerie d’applications Azure AD](active-directory-app-gallery-listing.md)
- [Envoyer des applications web pour Office 365 à Mon tableau de bord vendeur](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Présentation du manifeste d’application Azure Active Directory](active-directory-application-manifest.md)
- [Comprendre les instructions de personnalisation pour les boutons d’acquisition de connexion et d’application dans votre application cliente](active-directory-branding-guidelines.md)
- [Version préliminaire : comment créer des applications qui connectent les utilisateurs avec à la fois des comptes personnels et des comptes professionnels ou scolaires](active-directory-appmodel-v2-overview.md)
- [Version préliminaire : création d’applications qui inscrivent et connectent les consommateurs](../active-directory-b2c/active-directory-b2c-overview.md)


## Référence

Ces articles fournissent une référence de base pour les API REST et de bibliothèque d’authentification, les protocoles, les erreurs, les exemples de code et les points de terminaison.

###  Support
- [Questions balisées](http://stackoverflow.com/questions/tagged/azure-active-directory) : recherchez des solutions Azure Active Directory en cas de dépassement de la capacité de la pile en recherchant les balises [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).

### Code

- [Bibliothèques open source Azure Active Directory](http://github.com/AzureAD) : le moyen le plus simple pour rechercher la source d’une bibliothèque consiste à utiliser notre [liste de bibliothèques](active-directory-authentication-libraries.md).

- [Exemples Azure Active Directory](https://github.com/azure-samples?query=active-directory) : le moyen le plus simple de parcourir la liste d’exemples est d’utiliser l’[index des exemples de code](active-directory-code-samples.md).

- [ADAL pour .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx) : documentation de la bibliothèque d’authentification .NET.

### API Graph

- [Informations de référence sur l’API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx) : informations de référence REST pour l’API Azure Active Directory Graph. [Découvrez l’expérience interactive de référence de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Étendues d’autorisation de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) : étendues d’autorisation OAuth 2.0 utilisées pour contrôler l’accès d’une application aux données d’annuaire d’un client.

### Protocoles d’authentification et d’autorisation

- [Substitution des clés de signature dans Azure AD](active-directory-signing-key-rollover.md) : découvrez le rythme de substitution des clés d’Azure AD et apprenez à mettre à jour la clé pour les scénarios d’application les plus courants.

- [Protocole OAuth 2.0 : présentation de l’octroi implicite](active-directory-dev-understanding-oauth2-implicit-grant.md) : en savoir plus sur l’octroi d’autorisation implicite et découvrir s’il convient à votre application.

- [Référence du protocole SAML 2.0](active-directory-saml-protocol-reference.md) : le protocole SAML 2.0 permet aux applications de fournir une expérience d’authentification unique à leurs utilisateurs.

- [Protocole WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) : Azure Active Directory prend en charge le protocole WS-Federation 1.2, tel que spécifié dans la spécification Web Services Federation Version 1.2. Pour plus d’informations sur le document de métadonnées de fédération, consultez la page [Métadonnées de fédération](active-directory-federation-metadata.md).

- [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md) : vous pouvez utiliser ce guide pour comprendre et évaluer les revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT).

## Vidéos

### Créer

Ces présentations sur le développement d'applications à l'aide d'Azure Active Directory mettent en scène des intervenants qui travaillent directement dans l'équipe d'ingénierie. Les présentations couvrent les sujets fondamentaux, notamment IDMaaS, l'authentification, la fédération d'identités et l'authentification unique.

- [Microsoft Identity : état de l’union et orientation future](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
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

<!---HONumber=AcomDC_0713_2016-->