---
title: "Azure Active Directory pour les développeurs | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de la connexion des comptes professionnels et scolaires Microsoft à l’aide d’Azure Active Directory."
services: active-directory
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 0275bd00850a734c9b721c8348e24c5c40422201
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory pour les développeurs
Azure Active Directory est un service d’identité de cloud qui permet aux développeurs de connecter en toute sécurité les utilisateurs disposant d’un compte professionnel ou scolaire assuré par Microsoft.  La présente documentation explique comment ajouter une prise en charge d’Azure AD à votre application par le biais de protocoles d’authentification standard du secteur, OAuth et OpenID Connect.

| | |
| --- | --- |
|[Concepts de base de l’authentification](active-directory-authentication-scenarios.md) | Introduction à l’authentification avec Azure AD |
|[Types d’applications](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Vue d’ensemble des scénarios d’authentification pris en charge par Azure AD |                                
                                                                              
## <a name="get-started"></a>Prise en main
Ces configurations guidées vous accompagneront tout au long de l’utilisation de nos bibliothèques d’authentification afin de connecter les utilisateurs Azure Active Directory.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Applications mobiles et de bureau](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Applications mobiles et de bureau</center> | [Vue d'ensemble](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web Apps](./media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Vue d'ensemble](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Applications à page unique](./media/active-directory-developers-guide/SPA.png)<br />Applications à page unique</center> | [Vue d'ensemble](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![API Web](./media/active-directory-developers-guide/Web_API.png)<br />API Web</center> | [Vue d'ensemble](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![De service à service](./media/active-directory-developers-guide/Service_App.png)<br />De service à service</center> | [Vue d'ensemble](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Informations d’identification du client OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>Guides
Ces articles vous expliquent comment réaliser les tâches courantes avec Azure Active Directory.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Inscription d’application](active-directory-integrating-applications.md)           | Comment inscrire une application dans Azure AD |
|[Applications multi-locataires](active-directory-devhowto-multi-tenant-overview.md)    | Comment connecter n’importe quel compte professionnel Microsoft |
|[OAuth et OpenID Connect](active-directory-protocols-openid-connect-code.md)| Comment connecter des utilisateurs et appeler des API web à l’aide de nos protocoles d’authentification modernes |
|[Autres guides…](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>Référence
Ces articles fournissent des informations détaillées sur les API, les messages de protocole et les termes utilisés dans Azure Active Directory.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliothèques d’authentification](active-directory-authentication-libraries.md)   | Vue d’ensemble des bibliothèques et des kits de développement logiciel fournis par Azure AD |
| [Exemples de code](active-directory-code-samples.md)                                  | Liste de tous les exemples de code Azure AD |
| [Glossaire](active-directory-dev-glossary.md)                                      | Terminologie et définitions des termes utilisés dans ce document |
| [Autres documents de référence…](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>Aide et support
Ce sont les ressources les plus pertinentes pour obtenir de l’aide concernant le développement sur Azure Active Directory.

|  |  
|---|
|[Balises `azure-active-directory` et `adal` de Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Commentaires sur Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [Essayez Microsoft Dev Chat (gratuit pour une durée limitée)](http://aka.ms/devchat) |

<br />

> [!NOTE]
> Si vous devez connecter des comptes personnels Microsoft, vous souhaiterez peut-être utiliser le [point de terminaison Azure AD 2.0](active-directory-appmodel-v2-overview.md).  Le point de terminaison Azure AD 2.0 permet d’unifier des comptes personnels et professionnels Microsoft (à partir d’Azure AD) dans un système d’authentification unique.

