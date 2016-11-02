<properties
    pageTitle="Vue d’ensemble du point de terminaison v2.0 | Microsoft Azure"
    description="Introduction à la création d’applications avec une connexion de compte Microsoft et Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>


# <a name="sign-in-microsoft-account-&-azure-ad-users-in-a-single-app"></a>Connecter les utilisateurs de compte Microsoft et d’Azure AD dans une même application

Auparavant, un développeur d’application qui souhaitait prendre en charge à la fois les comptes Microsoft et Azure Active Directory devait opérer une intégration avec deux systèmes distincts.  Nous avons mis en place une nouvelle version d’API d’authentification qui vous permet de connecter des utilisateurs avec les deux types de comptes à l’aide du système Azure AD.  Ce système d’authentification convergé est appelé **le point de terminaison v2.0**.  Avec le point de terminaison v2.0 et moyennant une intégration unique, vous pouvez toucher un public qui représente plusieurs millions d’utilisateurs titulaires aussi bien de comptes personnels que professionnels/scolaires.

Les applications qui utilisent le point de terminaison v2.0 peuvent également consommer des API REST à partir de [Microsoft Graph](https://graph.microsoft.io) et [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) à l’aide d’un type de compte.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Prise en main
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Choisissez votre plateforme préférée dans la liste ci-dessous pour créer une application à l’aide de nos bibliothèques et infrastructures open source.  Vous pouvez également utiliser notre documentation du protocole OAuth 2.0 et OpenID Connect pour envoyer et recevoir les messages de protocole directement sans utiliser une bibliothèque d’authentification.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="what's-new"></a>Nouveautés
Les informations conceptuelles ici seront utiles pour comprendre ce qui est et ce qui n’est pas possible avec le point de terminaison v2.0.

- Découvrez les [différents types d’application que vous pouvez créer avec le point de terminaison v2.0](active-directory-v2-flows.md).
- Familiarisez-vous avec les [limites, restrictions et contraintes](active-directory-v2-limitations.md) du point de terminaison v2.0.
- Nous avons récemment ajouté la prise en charge des [étendues restreintes aux administrateurs](active-directory-v2-scopes.md) et de [l’octroi des informations d’identification du client OAuth2](active-directory-v2-protocols-oauth-client-creds.md).  Essayez-les !

## <a name="reference"></a>Référence
Les liens ci-dessous vous seront utiles pour explorer la plateforme en profondeur :

- Build 2016 : [Prise en main des identités Microsoft : authentification de niveau entreprise pour vos applications](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Pour obtenir une aide sur le dépassement de capacité de pile, utilisez les mots-clés [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- [Référence sur le protocole v2.0](active-directory-v2-protocols.md)
- [Référence sur le jeton v2.0](active-directory-v2-tokens.md)
- [Référence de la bibliothèque v2.0](active-directory-v2-libraries.md)
- [Étendues et consentement dans le point de terminaison v2.0](active-directory-v2-scopes.md)
- [Le portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com)
- [Informations de référence sur les API Office 365 REST](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)


<!--HONumber=Oct16_HO2-->


