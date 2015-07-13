<properties
   pageTitle="Guide du développeur Azure Active Directory"
   description="Guide complet sur les ressources destinées aux développeurs pour Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/10/2015"
   ms.author="mbaldwin"/>


# Guide du développeur Azure Active Directory

## Vue d'ensemble
En tant que plateforme de gestion des identités en tant que service (IDMaaS), Azure Active Directory fournit aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les fonctionnalités clés d’Azure Active Directory. Nous vous suggérons de les lire dans l’ordre ou de passer à la rubrique [Prise en main](#getting-started) si vous êtes prêt à examiner les choses de façon approfondie.


1. **[Avantages de l’intégration avec Azure AD](active-directory-how-to-integrate.md)** : découvrez pourquoi l’intégration avec Active Directory Azure offre la meilleure solution pour une connexion et une autorisation sécurisées.

1. **[Utilisation d’Azure AD pour la connexion](active-directory-authentication-scenarios.md)** : tirez parti de l’authentification simplifiée d’Azure Active Directory pour fournir une connexion à votre application.

1. **[Interrogation de l’annuaire](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: utilisez l’API Azure Active Directory Graph pour accéder par programme à Azure AD via les points de terminaison d’API REST.

1. **[Présentation du modèle d’application](https://msdn.microsoft.com/library/azure/dn151122.aspx)** : apprenez-en plus sur l’inscription de votre application et les instructions de personnalisation pour les applications mutualisées.

1. **[Bibliothèques](https://msdn.microsoft.com/library/azure/dn151135.aspx)** : facilitez l’authentification des utilisateurs pour l’obtention de jetons d’accès avec les bibliothèques d’authentification Azure.

Pour afficher les vues d’ensemble AAD présentées à la conférence Build 2015, consultez la section de [vidéos](#videos) ci-dessous.


## Prise en main

Ces didacticiels sont adaptés pour plusieurs plateformes et vous permettent de commencer rapidement le travail de développement avec Azure Active Directory. Vous devez préalablement [obtenir un client Azure Active Directory](active-directory-howto-tenant.md).

#### Guides de démarrage rapide pour les applications mobiles ou PC

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Windows Store](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####Guides de démarrage rapide des applications web ou des API web

- [Application web .NET](active-directory-devquickstarts-webapp-dotnet.md)
- [API web .NET](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.JS](active-directory-devquickstarts-webapi-nodejs.md)


## Procédures

Ces articles décrivent comment effectuer des tâches spécifiques avec Azure Active Directory (AD).

- [Obtention d’un client Azure AD](active-directory-howto-tenant.md)
- [Affichage de votre application dans la galerie d’applications Azure AD](active-directory-app-gallery-listing.md)
- [Prise en main des API Office 365 dans les applications](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Envoi d’applications web pour Office 365 à Mon tableau de bord vendeur](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)


## Référence

Ces articles fournissent une référence de base pour les API REST et de bibliothèque d’authentification, les protocoles, les erreurs, les exemples de code et les points de terminaison.

###  Support
- **[Où obtenir un support technique](http://stackoverflow.com/questions/tagged/azure-active-directory)** : trouvez des solutions Azure AD en cas de dépassement de capacité de la pile en recherchant les balises [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).

### Code

- **[Bibliothèques open source Azure AD](http://github.com/AzureAD)** : le moyen le plus simple pour rechercher la source d’une bibliothèque consiste à utiliser notre [liste de bibliothèques](https://msdn.microsoft.com/library/azure/dn151135.aspx).

- **[Exemples Azure AD](http://github.com/AzureADSamples)** : le moyen le plus simple pour parcourir la liste d’exemples consiste à utiliser l’[Index des exemples de code](active-directory-code-samples.md).


### API Graph

- **[Référence de l’API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)** : référence REST pour l’API Azure Active Directory Graph. [Découvrez la nouvelle expérience interactive de référence de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **[Étendues d’autorisation de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)** : étendues d’autorisation OAuth 2.0 qui sont utilisées pour contrôler l’accès aux données d’annuaire dans un client.


### Protocoles d’authentification

- **[Référence du protocole SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx)** : le protocole SAML 2.0 permet aux applications de fournir une expérience d’authentification unique à leurs utilisateurs.


- **[Référence du protocole OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)** : le protocole OAuth 2.0 vous permet d’autoriser l’accès aux applications web et aux API web dans votre client Azure AD.


- **[Référence du protocole OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx)** : le protocole OpenID Connect 1.0 étend OAuth 2.0 pour une utilisation comme protocole d’authentification.


- **[Référence du protocole WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx)** : le protocole WS-Federation 1.2, tel que spécifié dans la spécification Web Services Federation Version 1.2.

- **[Jetons de sécurité et réclamations pris en charge](active-directory-token-and-claims.md)** : guide pour la compréhension et l’évaluation des revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT).

## Vidéos

### Build 2015

Ces vues d’ensemble sur le développement d’applications avec Azure Active Directory présentent des intervenants travaillant directement au sein de l’équipe d’ingénierie et passent en revue les rubriques fondamentales telles que IDMaaS, l’authentification, la fédération d’identité et l’authentification unique.

- **[Azure Active Directory : gestion des identités en tant que service pour les applications modernes](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Développement d’applications web modernes avec Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Développement d’applications web natives avec Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) est une série hebdomadaire de vidéos qui vous proposent des entretiens de 10 à 15 minutes avec des experts sur divers sujets relatifs à Azure. Utilisez la fonctionnalité Filtre des services sur la page pour afficher toutes les vidéos Azure Active Directory.

- **[Azure Identity 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Azure Identity 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Azure Identity 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## Réseaux sociaux

- **[Blog de l’équipe Active Directory](http://blogs.technet.com/b/ad/)** : tenez-vous informé des derniers développements dans le monde d’Azure AD.

- **[Blog Azure AD Graph](http://blogs.msdn.com/b/aadgraphteam)** : informations sur l’API Azure AD Graph .

- **[Cloud Identity](http://www.cloudidentity.net)** : réflexions sur la gestion des identités en tant que service, par un directeur de programme Azure Active Directory.

- **[Azure AD sur Twitter](https://twitter.com/azuread)** : annonces Azure AD limitées à 140 caractères.

<!---HONumber=62-->