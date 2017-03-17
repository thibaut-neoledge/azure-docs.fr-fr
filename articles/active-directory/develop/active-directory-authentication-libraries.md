---
title: "Bibliothèques d’authentification Azure Active Directory | Microsoft Docs"
description: "La bibliothèque d&quot;authentification Azure AD (ADAL) permet aux développeurs d’applications clientes d’authentifier facilement les utilisateurs sur Active Directory (AD) en local ou sur le cloud, puis d&quot;obtenir des jetons d&quot;accès pour sécuriser les appels d&quot;API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/13/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b4bfd0288f611ba1f5f1c30d402614d34c473457
ms.lasthandoff: 03/15/2017


---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliothèques d’authentification d’Azure Active Directory
La bibliothèque d'authentification Azure AD (ADAL) permet aux développeurs d’applications clientes d’authentifier facilement les utilisateurs sur Active Directory (AD) en local ou sur le cloud, puis d'obtenir des jetons d'accès pour sécuriser les appels d'API. ADAL possède de nombreuses fonctionnalités qui facilitent l'authentification aux développeurs, notamment la prise en charge asynchrone, un cache de jeton configurable qui stocke les jetons d'accès et les jetons d'actualisation, l'actualisation automatique des jetons lorsqu'un jeton d'accès expire et qu’un jeton d'actualisation est disponible, et bien plus encore. En gérant l’essentiel de la complexité, la bibliothèque ADAL peut aider les développeurs à se concentrer sur la logique métier dans leur application et à sécuriser facilement les ressources sans être un expert de la sécurité.

La bibliothèque ADAL est disponible sur diverses plateformes.

### <a name="client-libraries"></a>Bibliothèques clientes

| Plateforme | Bibliothèque | Télécharger | Code source | Exemple | Référence
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS et Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Application de bureau](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[Référence](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| .NET Client, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [Application de bureau](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[Référence](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Application à page unique](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Application iOS](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [Référence](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[Référentiel Central](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Application Android](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Application web Java](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>Bibliothèques serveur 

| Plateforme | Bibliothèque | Télécharger | Code source | Exemple | Référence
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pour Azure AD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Application MVC](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pour OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Application web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[Github](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN pour WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Application Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensions de protocole d’identité pour .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Gestionnaire JWT pour .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>Scénarios
Voici trois scénarios courants dans lesquels ADAL peut être utilisée pour l'authentification.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Authentification des utilisateurs d'une application cliente sur une ressource distante
Dans ce scénario, un développeur a un client, par exemple une application WPF, qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le locataire Azure AD qu’utilise l’API web. Par conséquent, il peut utiliser la bibliothèque ADAL pour faciliter l'authentification avec Azure AD, soit en déléguant complètement l'expérience d'authentification à ADAL, soit en gérant de manière explicite les informations d'identification de l'utilisateur. La bibliothèque ADAL facilite l’authentification de l'utilisateur, l’obtention d’un jeton d'accès et d’un jeton d'actualisation d'Azure AD, puis l’utilisation du jeton d'accès pour effectuer des requêtes à l'API web.

Pour voir un exemple de code qui illustre ce scénario en utilisant l’authentification auprès d’Azure AD, consultez la section [Native Client WPF Application to Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Authentification d'une application serveur sur une ressource distante
Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. Il dispose d’un abonnement Azure, sait comment appeler le service en aval et connaît le locataire Azure AD qu’utilise l’API web. Par conséquent, il peut utiliser la bibliothèque ADAL pour faciliter l'authentification avec Azure AD en gérant de manière explicite les informations d'identification de l'application. La bibliothèque ADAL facilite la récupération d’un jeton d'Azure AD à l'aide d'informations d'identification client de l'application, puis l’utilisation de ce jeton pour faire des requêtes à l'API web. La bibliothèque ADAL gère également la durée de vie du jeton d'accès en le mettant en cache et en le renouvelant si nécessaire. Pour voir un exemple de code qui illustre ce scénario, consultez la section [Console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Authentification d'une application serveur pour le compte d'un utilisateur afin d'accéder à une ressource distante
Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. La demande doit également être effectuée au nom d'un utilisateur d’Azure AD. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le locataire Azure AD qu’utilise le service. Une fois l'utilisateur authentifié sur l'application web, l'application peut obtenir d’Azure AD un code d'autorisation pour l'utilisateur. L'application web peut ensuite utiliser ADAL pour obtenir d’Azure AD un jeton d'accès et un jeton d’actualisation pour le compte de l'utilisateur en utilisant le code d'autorisation et les informations d’identification client associés à l'application. Une fois l'application web en possession du jeton d'accès, elle peut appeler l'API web jusqu’à expiration du jeton. Lorsque le jeton expire, l'application web peut utiliser la bibliothèque ADAL pour obtenir un nouveau jeton d'accès en utilisant le jeton d’actualisation reçu précédemment.

## <a name="see-also"></a>Voir aussi
[Le guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Scénarios d’authentification pour Azure Active Directory](active-directory-authentication-scenarios.md)

[Exemples de code Azure Active Directory](active-directory-code-samples.md)

