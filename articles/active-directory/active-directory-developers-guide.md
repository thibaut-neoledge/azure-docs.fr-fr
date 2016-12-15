---
title: "Guide du développeur Azure Active Directory | Microsoft Docs"
description: "Cet article fournit un guide complet sur les ressources destinées aux développeurs pour Azure Active Directory."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3e587c9422e008a7b3c46e34fed4d0f1b2a8189c


---
# <a name="azure-active-directory-developers-guide"></a>Guide du développeur Azure Active Directory
## <a name="overview"></a>Vue d'ensemble
En tant que plateforme de gestion des identités en tant que service (IDMaaS), Azure Active Directory (AD) fournit aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les fonctionnalités clés d’Azure AD. Nous vous suggérons de les lire dans l’ordre ou de passer à la rubrique [Prise en main](#getting-started) si vous êtes prêt à examiner les choses de façon approfondie.

1. [Avantages de l’intégration d’Azure AD](develop/active-directory-how-to-integrate.md): découvrez pourquoi l’intégration dans Azure Active Directory constitue la meilleure solution pour sécuriser une connexion et une autorisation.
2. [Scénarios d’authentification Azure AD](active-directory-authentication-scenarios.md): profitez de l’authentification simplifiée d’Azure AD pour fournir une connexion à votre application.
3. [Intégration d’applications dans Azure Active Directory](active-directory-integrating-applications.md): découvrez comment ajouter, mettre à jour et supprimer des applications dans Azure AD, ainsi que les directives de personnalisation pour les applications intégrées.
4. [API Graph Azure d’Active Directory](active-directory-graph-api.md): utilisez l’API Graph d’Azure AD pour accéder par programme à Azure AD à l’aide des points de terminaison de l’API REST. L’API Graph d’Azure AD est également accessible via [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fournit une API unifiée qui permet d’accéder à plusieurs API du service cloud Microsoft via un point de terminaison de l’API REST et avec un jeton d’accès unique.
5. [Bibliothèques d’authentification Azure AD](active-directory-authentication-libraries.md): facilitez l’authentification des utilisateurs pour l’obtention de jetons d’accès à l’aide des bibliothèques d’authentification AD Azure pour .NET, JavaScript, Objective-C, Android, etc.

## <a name="getting-started"></a>Prise en main
Ces didacticiels sont adaptés pour plusieurs plateformes et vous aident à commencer rapidement le travail de développement avec Azure Active Directory. Vous devez préalablement [obtenir un client Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guides de démarrage rapide pour les applications mobiles et PC
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | [![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows Universal](active-directory-devquickstarts-windowsstore.md) |[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Intégrer directement à OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Guides de démarrage rapide pour les applications web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) | [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.JS](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md) |[Intégrer directement à OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Guides de démarrage rapide pour les API web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.JS](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.JS](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>Interrogation du guide de démarrage rapide du répertoire
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [API Graph](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Procédures
Ces articles décrivent comment effectuer des tâches spécifiques avec Azure Active Directory :

* [Obtention d’un locataire Azure AD](active-directory-howto-tenant.md)
* [Connecter un utilisateur Azure AD à l’aide du modèle d’application mutualisée](active-directory-devhowto-multi-tenant-overview.md)
* Activer l’authentification unique entre applications en utilisant ADAL, sur [Android](active-directory-sso-android.md) et sur les appareils [iOS](active-directory-sso-ios.md)
* [Certifier votre application AppSource pour Azure AD](active-directory-devhowto-appsource-certified.md)
* [Afficher votre application dans la galerie d’applications Azure AD](active-directory-app-gallery-listing.md)
* [Envoyer des applications web pour Office 365 à Mon tableau de bord vendeur](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Présentation du manifeste d’application Azure Active Directory](active-directory-application-manifest.md)
* [Comprendre les instructions de personnalisation pour les boutons d’acquisition de connexion et d’application dans votre application cliente](active-directory-branding-guidelines.md)
* [Version préliminaire : comment créer des applications qui connectent les utilisateurs avec à la fois des comptes personnels et des comptes professionnels ou scolaires](active-directory-appmodel-v2-overview.md)
* [Version préliminaire : création d’applications qui inscrivent et connectent les consommateurs](../active-directory-b2c/active-directory-b2c-overview.md)
* [Version préliminaire : configuration de la durée de vie des jetons dans Azure AD](active-directory-configurable-token-lifetimes.md) à l’aide de PowerShell. Consultez [Opérations de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) et [Entité de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) pour plus d’informations sur la configuration de l’API Azure AD Graph.

## <a name="reference"></a>Référence
Ces articles fournissent une référence de base pour les API REST et de bibliothèque d’authentification, les protocoles, les erreurs, les exemples de code et les points de terminaison.  

### <a name="support"></a>Support
* [Questions balisées](http://stackoverflow.com/questions/tagged/azure-active-directory) : recherchez des solutions Azure Active Directory en cas de dépassement de la capacité de la pile en recherchant les balises [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).
* Consultez le [Glossaire du développeur Azure Active Directory](active-directory-dev-glossary.md) pour obtenir la définition de termes couramment utilisés dans le développement et l’intégration d’applications.

### <a name="code"></a>Code
* [Bibliothèques open source Azure Active Directory](http://github.com/AzureAD) : le moyen le plus simple pour rechercher la source d’une bibliothèque consiste à utiliser notre [liste de bibliothèques](active-directory-authentication-libraries.md).
* [Exemples Azure Active Directory](https://github.com/azure-samples?query=active-directory) : le moyen le plus simple de parcourir la liste d’exemples est d’utiliser l’[index des exemples de code](active-directory-code-samples.md).
* [ADAL (Active Directory Authentication Library) pour .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - La documentation de référence est disponible pour [la dernière version principale](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) et [la version principale précédente](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API Graph
* [Informations de référence sur l’API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): informations de référence REST pour l’API Azure Active Directory Graph. [Découvrez l’expérience interactive de référence de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Étendues d’autorisation de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): étendues d’autorisation OAuth 2.0 utilisées pour contrôler l’accès d’une application aux données d’annuaire d’un client.

### <a name="authentication-and-authorization-protocols"></a>Protocoles d’authentification et d’autorisation
* [Substitution des clés de signature dans Azure AD](active-directory-signing-key-rollover.md): découvrez le rythme de substitution des clés de signature Azure AD et apprenez à mettre à jour la clé dans les scénarios d’application les plus courants.
* [Protocole OAuth 2.0 : utilisation de l’octroi de code d’autorisation](active-directory-protocols-oauth-code.md): vous pouvez utiliser l’octroi de code d’autorisation du protocole OAuth 2.0 pour autoriser l’accès aux applications web et aux API web dans votre client Azure Active Directory.
* [Protocole OAuth 2.0 : présentation de l’octroi implicite](active-directory-dev-understanding-oauth2-implicit-grant.md): découvrez l’octroi d’autorisation implicite et vérifiez s’il convient à votre application.
* [Protocole OAuth 2.0 : appels de service à service à l’aide des informations d’identification du client](active-directory-protocols-oauth-service-to-service.md): l’octroi des informations d’identification du client OAuth 2.0 permet à un service web (client confidentiel) d’utiliser ses propres informations d’identification pour s’authentifier lorsqu’il appelle un autre service web, au lieu d’emprunter l’identité d’un utilisateur. Dans ce scénario, le client est généralement un service web de niveau intermédiaire, un service démon ou un site web.
* [Protocole OpenID Connect 1.0 : connexion et authentification](active-directory-protocols-openid-connect-code.md): le protocole OpenID Connect 1.0 permet d’utiliser OAuth 2.0 comme protocole d’authentification. Une application cliente peut recevoir un id_token pour gérer le processus de connexion ou augmenter le flux du code d’autorisation pour recevoir à la fois un id_token et le code d’autorisation.
* [Référence du protocole SAML 2.0](active-directory-saml-protocol-reference.md): le protocole SAML 2.0 permet aux applications de fournir une expérience d’authentification unique à leurs utilisateurs.
* [Protocole WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory prend en charge le protocole WS-Federation 1.2 conformément à la spécification Web Services Federation Version 1.2. Pour plus d’informations sur le document de métadonnées de fédération, consultez la page [Métadonnées de fédération](active-directory-federation-metadata.md).
* [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md): vous pouvez utiliser ce guide pour comprendre et évaluer les revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT).

## <a name="videos"></a>Vidéos
### <a name="build"></a>Créer
Ces présentations sur le développement d'applications à l'aide d'Azure Active Directory mettent en scène des intervenants qui travaillent directement dans l'équipe d'ingénierie. Les présentations couvrent les sujets fondamentaux, notamment IDMaaS, l’authentification, la fédération d’identités et l’authentification unique.

* [Microsoft Identity : état de l’union et orientation future](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory : gestion des identités en tant que service pour les applications modernes](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Développement d’applications web modernes avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Développement d’applications web natives avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) est une série hebdomadaire de vidéos au format 1:1 qui vous propose de courts entretiens (10 à 15 minutes) avec des experts sur divers sujets relatifs à Azure.  Utilisez la fonctionnalité Filtre des services sur la page pour afficher toutes les vidéos Azure Active Directory.

* [Identité Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Identité Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Identité Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Réseaux sociaux
* [Blog de l’équipe Active Directory](http://blogs.technet.com/b/ad/): les derniers développements dans l’univers d’Azure Active Directory.
* [Blog de l’équipe Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informations Azure Active Directory spécifiques à l’API Graph.
* [Cloud Identity](http://www.cloudidentity.net): réflexions sur la gestion des identités en tant que service, par un directeur de programme Azure Active Directory.  
* [Azure Active Directory sur Twitter](https://twitter.com/azuread): annonces Azure Active Directory de moins de 140 caractères.

## <a name="windows-server-on-premises-development"></a>Développement Windows Server sur site
Pour obtenir des conseils sur l’utilisation d’un développement Windows Server et Active Directory Federation Services (ADFS), consultez :

* [Scénarios AD FS pour développeurs](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers) : fournit une vue d’ensemble des composants AD FS et de leur fonctionnement, avec des détails sur les scénarios d’authentification/autorisation pris en charge.
* [Procédures pas à pas AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): liste d’articles de procédure pas à pas fournissant des instructions détaillées sur l’implémentation des flux d’authentification/autorisation connexes.




<!--HONumber=Dec16_HO2-->


