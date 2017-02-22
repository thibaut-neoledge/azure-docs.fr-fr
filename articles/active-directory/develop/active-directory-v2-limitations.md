---
title: "Azure Active Directory v2.0 : limites et restrictions du point de terminaison | Microsoft Docs"
description: "Une liste des limitations et restrictions associées au point de terminaison v2.0 Azure AD."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 5d1ceabeeee8cef0170b928703488845f70656ef


---
# <a name="should-i-use-the-v20-endpoint"></a>Dois-je utiliser le point de terminaison v2.0 ?
Quand vous créez des applications qui s’intègrent à Azure Active Directory (Azure AD), vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 répondent à vos besoins. Le point de terminaison Azure AD d’origine est toujours intégralement pris en charge. À certains égards, il est plus riche en fonctionnalités que le point de terminaison v2.0. Toutefois, le point de terminaison v2.0 [présente des avantages significatifs](active-directory-v2-compare.md) pour les développeurs. Les avantages de la version 2.0 peuvent vous inciter à utiliser le nouveau modèle de programmation.

Voici notre recommandation concernant l’utilisation du point de terminaison v2.0 :

* Si vous souhaitez prendre en charge des comptes personnels Microsoft dans votre application, utilisez le point de terminaison v2.0. Avant cela, veillez à bien comprendre les limitations abordées dans cet article, en particulier celles qui s’appliquent aux comptes professionnels et scolaires.
* Si votre application doit prendre en charge uniquement les comptes professionnels et scolaires, utilisez [les points de terminaison Azure AD d’origine](active-directory-developers-guide.md).

Au fil du temps, le point de terminaison v2.0 se développera et les restrictions répertoriées ici seront éliminées. Ainsi, vous n’aurez qu’à utiliser le point de terminaison v2.0. En attendant, cet article vous aide à déterminer si le point de terminaison v2.0 répond à vos besoins. Nous continuerons à mettre à jour cet article pour refléter l’état actuel du point de terminaison v2.0. Consultez-le régulièrement pour réévaluer vos besoins par rapport aux fonctionnalités de la version 2.0.

Si vous disposez d’une application associée à Azure AD qui ne recourt pas au point de terminaison v2.0, il n’est pas nécessaire de repartir de zéro. À l’avenir, nous vous fournirons un moyen d’utiliser vos applications Azure AD existantes avec le point de terminaison v2.0.

## <a name="restrictions-on-app-types"></a>Restrictions concernant les types d’applications
Actuellement, les types d’applications suivants ne sont pas pris en charge par le point de terminaison v2.0. Pour obtenir une description des types d’applications pris en charge, consultez [Types d’applications pour le point de terminaison Azure Active Directory v2.0](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>API Web autonome
Vous pouvez utiliser le point de terminaison v2.0 pour [générer une API web sécurisée avec OAuth 2.0](active-directory-v2-flows.md#web-apis). Toutefois, cette API web peut recevoir uniquement les jetons d’une application ayant le même ID d’application. Vous ne pouvez pas accéder à une API web à partir d’un client qui a un ID d’application différent. Ce client ne pourra pas demander ou obtenir d’autorisation d’accès à votre API web.

Pour voir comment créer une API web qui accepte des jetons d’un client ayant un ID d’application identique, consultez les exemples d’API web de point de terminaison v2.0 de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

### <a name="web-api-on-behalf-of-flow"></a>Flux On-Behalf-Of d’API web
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par le point de terminaison v2.0. Ce scénario est courant dans les clients natifs qui disposent d’une API web principale, qui à son tour appelle une instance de Microsoft Online Services ou une autre API web personnalisée qui prend en charge Azure AD.

Vous pouvez prendre en charge ce scénario à l’aide de la concession des informations d’identification du porteur OAuth 2.0 JSON Web Token (JWT), également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n’est actuellement pas pris en charge pour le point de terminaison v2.0. Pour observer le fonctionnement de ce flux dans le service Azure AD disponible généralement, consultez l’ [exemple de code On-Behalf-Of sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application
À l’heure actuelle, pour chaque application que vous souhaitez intégrer au point de terminaison v2.0, vous devez créer une inscription d’application dans le nouveau [portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Les applications de compte Microsoft ou Azure AD existantes ne sont pas compatibles avec le point de terminaison v2.0. Les applications qui sont inscrites dans un portail autre que le portail d’inscription des applications ne sont pas compatibles avec le point de terminaison v2.0. À l’avenir, nous prévoyons de fournir un moyen d’utiliser des applications existantes en tant qu’applications v2.0. Cependant, il n’existe actuellement aucun chemin de migration permettant à une application existante de fonctionner avec le point de terminaison v2.0.

Les applications inscrites dans le portail d’inscription des applications ne fonctionneront pas avec le point de terminaison d’authentification d’origine Azure AD. Vous pouvez toutefois utiliser les applications créées dans le portail d’inscription des applications pour procéder à une intégration avec le point de terminaison d’authentification du compte Microsoft `https://login.live.com`.

De plus, les inscriptions d’applications que vous créez dans le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ont les caractéristiques suivantes :

* La propriété **homepage**, également appelée *URL de connexion*, n’est pas prise en charge. Sans page d’accueil, ces applications n’apparaîtront pas dans le volet Office MyApps.
* Actuellement, seuls deux secrets d’application sont autorisés par ID d’application.
* Une inscription d’application ne peut être affichée et gérée que par un seul compte de développeur. Elle ne peut pas être partagée entre plusieurs développeurs.
* Il existe plusieurs restrictions quant au format de l’URI de redirection autorisé. Pour plus d’informations sur les URI de redirection, consultez la section suivante.

## <a name="restrictions-on-redirect-uris"></a>Restrictions concernant les URI de redirection
Actuellement, les applications inscrites dans le portail d’inscription des applications sont limitées à un jeu restreint de valeurs d’URI de redirection. L’URI de redirection pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URI de redirection doivent partager un seul domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web ayant l’un des ces URI de redirection :

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URI de redirection existante au nom DNS de l’URI de redirection que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :  

* Le nom DNS complet de la nouvelle URI de redirection ne correspond pas au nom DNS de l’URI de redirection existante.
* Le nom DNS complet de la nouvelle URI de redirection n’est pas un sous-domaine de l’URI de redirection existante.

Par exemple, si l’application a cet URI de redirection :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous souhaitez disposer d’une application avec login-east.contoso.com et login-west.contoso.com en tant qu’URI de redirection, vous devez ajouter ces URI de redirection dans l’ordre suivant :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Vous pouvez ajouter les deux derniers car il s’agit de sous-domaines du premier URI de redirection, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Pour savoir comment inscrire une application dans le portail d’inscription des applications, consultez [Inscription d’une application avec le point de terminaison v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restrictions concernant les services et API
Actuellement, le point de terminaison v2.0 prend en charge la connexion de toute application inscrite dans le portail d’inscription des applications et figurant dans la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md). Toutefois, ces applications peuvent obtenir des jetons d’accès OAuth 2.0 pour un ensemble très limité de ressources. Le point de terminaison v2.0 délivre des jetons d’accès uniquement pour :

* L’application qui a demandé le jeton. Une application peut obtenir un jeton d’accès pour son propre compte, si l’application logique est composée de plusieurs composants ou niveaux. Pour voir ce scénario en action, consultez nos didacticiels [Prise en main](active-directory-appmodel-v2-overview.md#getting-started) .
* La messagerie Outlook, le calendrier et les API REST de Contacts, qui se trouvent à l’adresse https://outlook.office.com. Pour savoir comment écrire une application qui accède à ces API, consultez les didacticiels de [Prise en main d’Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
* Les API Microsoft Graph. Vous pouvez en savoir plus sur [Microsoft Graph](https://graph.microsoft.io) et les données qui vous sont accessibles.

Aucun autre service n’est actuellement pris en charge. Davantage de services Microsoft Online seront ajoutés prochainement, en plus de la prise en charge de vos propres services et API web personnalisés.

## <a name="restrictions-on-libraries-and-sdks"></a>Restrictions concernant les bibliothèques et les SDK
Actuellement, la prise en charge des bibliothèques pour le point de terminaison v2.0 est limitée. Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

* Si vous générez une application web, vous pouvez en toute sécurité utiliser le middleware Microsoft mis à la disposition générale côté serveur pour vous connecter et procéder à la validation des jetons. Vous recourrez notamment au middleware OWIN Open ID Connect pour ASP.NET et au plug-in Node.js Passport. Pour obtenir des exemples de code qui utilisent le middleware Microsoft, consultez la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).
* Pour d’autres plateformes et pour les applications natives et mobiles, vous pouvez procéder à l’intégration avec le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans votre code d’application. Les protocoles v2.0 OpenID Connect et OAuth [sont explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
* Pour finir, vous pouvez utiliser les bibliothèques open source Open ID Connect et OAuth pour procéder à l’intégration avec le point de terminaison v2.0. Le protocole v2.0 devrait être compatible avec de nombreuses bibliothèques de protocole open source sans modification majeure. La disponibilité de ces types de bibliothèques varie en fonction de la langue et de la plateforme. Les sites web [Open ID Connect](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) contiennent une liste à jour des implémentations les plus courantes. Pour plus d’informations, consultez [Azure Active Directory v2.0 et bibliothèques d’authentification](active-directory-v2-libraries.md), ainsi que la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison v2.0.

Nous avons également publié une préversion de la [bibliothèque d’authentification Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) uniquement pour .NET. N’hésitez pas à essayer cette bibliothèque dans les applications de serveur et clientes .NET. Toutefois, en tant que bibliothèque en préversion, elle n’est pas accompagnée d’une prise en charge de qualité générale.

## <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles
Le point de terminaison v2.0 prend en charge uniquement Open ID Connect et OAuth 2.0. Toutefois, certaines des fonctionnalités de ces protocoles n’ont pas été intégrées dans le point de terminaison v2.0.

Les fonctionnalités et caractéristiques de protocole classiques suivantes ne sont *pas disponibles* actuellement dans le point de terminaison v2.0 :

* Le paramètre `end_session_endpoint` OpenID Connect, qui permet à une application de mettre fin à la session de l’utilisateur, n’est pas disponible avec le point de terminaison v2.0.
* Les jetons d’ID délivrés par le point de terminaison v2.0 ont uniquement un identificateur par paire pour l’utilisateur. Cela signifie que deux applications différentes reçoivent des ID différents pour le même utilisateur. Notez que, en interrogeant le point de terminaison Microsoft Graph `/me`, vous pouvez obtenir un ID concordant pour l’utilisateur que vous pourrez utiliser dans plusieurs applications.
* Les jetons d’ID délivrés par le point de terminaison v2.0 ne contiennent pas de revendication `email` pour l’utilisateur, même si vous obtenez l’autorisation de l’utilisateur de consulter sa messagerie.
* Le point de terminaison OpenID Connect UserInfo n’est pas implémenté sur le point de terminaison v2.0. Toutefois, toutes les données de profil utilisateur que vous êtes susceptible de recevoir sur ce point de terminaison sont disponibles sur le point de terminaison Microsoft Graph `/me` .
* Le point de terminaison v2.0 ne prend pas en charge l’émission de revendications de rôle ou de groupe dans les jetons d’ID.

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le point de terminaison v2.0, consultez notre page de [référence sur les protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrictions concernant les comptes professionnels et scolaires
Quelques fonctionnalités propres aux utilisateurs d’entreprise Microsoft ne sont pas encore prises en charge par le point de terminaison v2.0. Pour plus d’informations, lisez les sections suivantes.

### <a name="device-based-conditional-access-native-and-mobile-apps-and-microsoft-graph"></a>Accès conditionnel basé sur les appareils, applications natives et mobiles, et Microsoft Graph
Le point de terminaison v2.0 ne prend pas encore en charge l’authentification des appareils pour les applications mobiles et natives, telles que les applications natives qui s’exécutent sur iOS ou Android. Pour certaines organisations, ceci peut empêcher votre application native d’appeler Microsoft Graph. L’authentification des appareils est obligatoire lorsqu’un administrateur définit pour une application une stratégie d’accès conditionnel basé sur les appareils. Pour le point de terminaison v2.0, le scénario le plus probable pour l’accès conditionnel basé sur les appareils consiste à ce qu’un administrateur définisse une stratégie sur une ressource dans Microsoft Graph, comme l’API Outlook. Si un administrateur définit cette stratégie et que votre application native demande un jeton à Microsoft Graph, la demande échoue car l’authentification des appareils n’est pas encore prise en charge. Cependant, les applications web qui demandent des jetons à Microsoft Graph sont prises en charge quand des stratégies basées sur les appareils sont configurées. Dans le cas de l’application web, l’authentification des appareils est effectuée par le biais du navigateur web de l’utilisateur.

En tant que développeur, vous n’avez sans doute aucun contrôle sur le moment où les stratégies sont définies sur des ressources Microsoft Graph. Vous ne savez sans doute même pas quand cela se produit. Si vous créez une application pour des utilisateurs professionnels et scolaires, utilisez [le point de terminaison Azure AD d’origine](active-directory-developers-guide.md) jusqu’à ce que le point de terminaison v2.0 prenne en charge l’authentification des appareils. Vous pouvez en savoir plus sur [l’accès conditionnel basé sur les appareils dans Azure AD](../active-directory-conditional-access.md#device-based-conditional-access).

### <a name="windows-integrated-authentication-for-federated-tenants"></a>Authentification Windows intégrée pour les clients fédérés
Si vous avez utilisé Active Directory Authentication Library (ADAL) (avec le point de terminaison Azure AD d’origine) dans des applications Windows, vous aurez peut-être tiré parti de ce que l’on appelle l’octroi d’assertion SAML (Security Assertion Markup Language). Avec cet octroi, les utilisateurs de locataires Azure AD fédérés peuvent s’authentifier en mode silencieux auprès de leur instance d’Active Directory locale sans entrer leurs informations d’identification. À l’heure actuelle, l’octroi d’assertion SAML n’est pas pris en charge sur le point de terminaison v2.0.




<!--HONumber=Jan17_HO3-->


