---
title: "Utiliser l’authentification Azure AD pour accéder à l’API Azure Media Services avec REST | Microsoft Docs"
description: "Découvrez comment accéder à une API Azure Media Services avec l’authentification Azure Active Directory par le biais de REST."
services: media-services
documentationcenter: 
author: willzhan
manager: SyntaxC4
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a4531b69c44337c4863016810123f7f89bf7f98f
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---

# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Utiliser l’authentification Azure AD pour accéder à l’API Azure Media Services avec REST

L’équipe Azure Media Services a publié le support de l’authentification Azure Active Directory (Azure AD) pour accéder à Azure Media Services. Elle a également annoncé des plans visant à déconseiller l’authentification du service Azure Access Control pour l’accès à Media Services. Étant donné tous les abonnements Azure et comptes Media Services sont joints à un locataire Azure AD, le support de l’authentification Azure AD apporte de nombreux avantages en termes de sécurité. Pour plus d’informations sur cette modification et la migration (si vous utilisez le Kit de développement logiciel (SDK) Media Services .NET pour votre application), consultez les billets de blog et articles suivants :

- [Azure Media Services announces support for Azure AD and deprecation of Access Control authentication](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation) (Azure Media Services annonce la prise en charge d’Azure AD et déconseille l’authentification Access Control)
- [Accéder à l’API Azure Media Services avec l’authentification Azure AD](media-services-use-aad-auth-to-access-ams-api.md)
- [Use Azure AD authentication to access Azure Media Services API by using Microsoft .NET](media-services-dotnet-get-started-with-aad.md) (Utiliser l’authentification Azure AD pour accéder à l’API Azure Media Services avec Microsoft .NET)
- [Prise en main de l’authentification Azure AD à l’aide du portail Azure](media-services-portal-get-started-with-aad.md)

Certains clients doivent développer leurs solutions Media Services sous les contraintes suivantes :

*   Ils utilisent un langage de programmation qui n’est pas Microsoft .NET ou C#, ou l’environnement d’exécution n’est pas Windows.
*   Les bibliothèques Azure AD telles que les bibliothèques d’authentification Active Directory ne sont pas disponibles pour le langage de programmation ou ne peuvent pas être utilisées pour leur environnement d’exécution.

Certains clients ont développé des applications à l’aide de l’API REST pour l’authentification Access Control et l’accès à Azure Media Services. Pour ces clients, vous avez besoin d’un moyen d’utiliser uniquement l’API REST pour l’authentification Azure AD et l’accès à Azure Media Services. Vous ne devez pas vous appuyer sur les bibliothèques Azure Active Directory ni sur le Kit de développement logiciel (SDK) Media Services .NET. Dans cet article, nous décrivons une solution et fournissons des exemples de code pour ce scénario. Étant donné que le code concerne tous les appels d’API REST, sans dépendance à aucune bibliothèque Azure AD ou Azure Media Services, il peut facilement être converti en n’importe quel autre langage de programmation.

> [!IMPORTANT]
> À l’heure actuelle, Media Services prend en charge le modèle d’authentification des services Azure Access Control. Toutefois, l’authentification Access Control sera déconseillée à compter du 1er juin 2018. Nous vous recommandons de migrer vers le modèle d’authentification Azure AD dès que possible.


## <a name="design"></a>Conception

Dans cet article, nous utilisons la conception d’authentification et d’autorisation suivante :

*  Protocole d’autorisation : OAuth 2.0. OAuth 2.0 est une norme de sécurité web qui couvre l’authentification et l’autorisation. Elle est prise en charge par Google, Microsoft, Facebook et PayPal. Elle a été ratifiée en octobre 2012. Microsoft prend bien en charge OAuth 2.0 et OpenID Connect. Ces deux normes sont prises en charge dans plusieurs services et bibliothèques clientes, notamment Azure Active Directory, Open Web Interface for .NET (OWIN) Katana, et les bibliothèques Azure AD.
*  Type d’octroi : type d’octroi des informations d’identification du client. Les informations d’identification du client constituent l’un des quatre types d’octroi dans OAuth 2.0. Ce type d’octroi est souvent utilisé pour l’accès à l’API Azure AD Microsoft Graph.
*  Mode d’authentification : principal de service. L’authentification peut également être faite par l’utilisateur ou de manière interactive.

Quatre applications ou services au total sont impliqués dans le flux d’authentification et d’autorisation Azure AD pour l’utilisation de Media Services. Les applications et services, ainsi que le flux, sont décrits dans le tableau suivant :

|Type d’application |Application |Flux|
|---|---|---|
|Client | Application ou solution cliente | Cette application (en fait, son proxy) est inscrite dans le locataire Azure AD dans lequel résident l’abonnement Azure et le compte Media Services. Le principal de service de l’application inscrite reçoit ensuite le rôle Propriétaire ou Collaborateur dans le contrôle d’accès (IAM) du compte Media Services. Le principal de service est représenté par l’ID client de l’application et la clé secrète client. |
|Fournisseur d’identité (IDP) | Azure AD en tant que fournisseur d’identité | Le principal de service de l’application inscrite (ID client et clé secrète client) est authentifié par Azure AD en tant que fournisseur d’identité. Cette authentification est effectuée en interne et de manière implicite. Comme dans les flux d’informations d’identification du client, c’est le client qui est authentifié, au lieu de l’utilisateur. |
|Secure Token Service (STS)/serveur OAuth | Azure AD en tant que STS | Une fois l’authentification par le fournisseur d’identité (ou le serveur OAuth pour OAuth 2.0) effectuée, un jeton d’accès ou un jeton JSON Web Token (JWT) est émis par Azure AD en tant que STS/serveur OAuth pour l’accès à la ressource de niveau intermédiaire. Dans le cas présent, il s’agit du point de terminaison de l’API REST Media Services. |
|Ressource | API REST Media Services | Chaque appel d’API REST Media Services est autorisé par un jeton d’accès qui est émis par Azure AD en tant que STS ou serveur OAuth. |

Si vous exécutez l’exemple de code et capturez un JWT ou un jeton d’accès, le JWT présente les attributs suivants :

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

Voici les mappages entre les attributs JWT et les quatre applications ou services du tableau précédent :

|Type d’application |Application |Attribut JWT |
|---|---|---|
|Client |Application ou solution cliente |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". ID client d’une application que vous allez inscrire dans Azure AD, dans la section suivante. |
|Fournisseur d’identité (IDP) | Azure AD en tant que fournisseur d’identité |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/".  Le GUID désigne l’ID du locataire Microsoft (microsoft.onmicrosoft.com). Chaque locataire a son propre ID unique. |
|Secure Token Service (STS)/serveur OAuth |Azure AD en tant que STS | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". Le GUID désigne l’ID du locataire Microsoft (microsoft.onmicrosoft.com). |
|Ressource | API REST Media Services |aud: "https://rest.media.azure.net". Destinataire ou audience du jeton d’accès. |

## <a name="steps-for-setup"></a>Étapes de configuration

Pour inscrire et configurer une application Azure AD concernant l’authentification Azure AD, et pour obtenir un jeton d’accès afin d’appeler le point de terminaison de l’API REST Azure Media Services, procédez comme suit :

1.  Dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), inscrivez une application Azure AD (par exemple, wzmediaservice) sur le locataire Azure AD (par exemple, microsoft.onmicrosoft.com). Peu importe si l’inscription est faite en tant qu’application web ou application native. En outre, vous pouvez choisir n’importe quelle URL de connexion et URL de réponse (par exemple, http://wzmediaservice.com pour les deux).
2. Dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), cliquez sur l’onglet **Configurer** de votre application. Notez l’**ID client**. Ensuite, sous **Clés**, générez une **clé client** (clé secrète client). 

    > [!NOTE] 
    > Prenez note de la clé secrète client. Elle ne s’affichera plus.
    
3.  Dans le [portail Azure](http://ms.portal.azure.com), accédez à votre compte Media Services. Sélectionnez le volet **Contrôle d’accès (IAM)**. Ajoutez un membre qui a le rôle Propriétaire ou Collaborateur. Pour le principal, recherchez le nom de l’application que vous avez inscrite à l’étape 1 (dans cet exemple, wzmediaservice).

## <a name="info-to-collect"></a>Informations à collecter

Pour préparer le codage REST, collectez les points de données suivants à inclure dans le code :

*   Azure AD en tant que point de terminaison STS : https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. À partir de ce point de terminaison, un jeton d’accès JWT est demandé. Outre son utilisation en tant qu’IDP, Azure AD sert en tant que STS. Azure AD émet un JWT pour l’accès aux ressources (jeton d’accès). Un jeton JWT a plusieurs revendications.
*   APIT REST Azure Media Services en tant que ressource ou audience : https://rest.media.azure.net.
*   ID client : consultez l’étape 2 dans [Étapes de configuration](#steps-for-setup).
*   Clé secrète client : consultez l’étape 2 dans [Étapes de configuration](#steps-for-setup).
*   Point de terminaison de l’API REST de votre compte Media Services dans le format suivant :

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    Il s’agit du point de terminaison vis-à-vis duquel tous les appels d’API REST Media Services dans votre application sont effectués. Par exemple, https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

Vous pouvez ensuite insérer ces cinq paramètres dans votre fichier web.config ou app.config, pour les utiliser dans votre code.

## <a name="sample-code"></a>Exemple de code

L’exemple de code se trouve dans [Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln) (Authentification Azure AD pour l’accès à Azure Media Services : par le biais d’une API REST).

L’exemple de code comporte deux parties :

*   Un projet de bibliothèque DLL contenant tout le code de l’API REST pour l’authentification et l’autorisation Azure AD. Il présente également une méthode pour effectuer des appels API REST au point de terminaison de l’API REST Media Services, avec le jeton d’accès.
*   Un client test de console, qui lance l’authentification Azure AD et appelle une autre API REST Media Services.

L’exemple de projet a trois fonctionnalités :

*   Authentifications AD Azure par l’octroi d’informations d’identification client uniquement à l’aide de l’API REST.
*   Accès à Media Services Azure uniquement à l’aide de l’API REST.
*   Accès au stockage Azure uniquement à l’aide de l’API REST (tel qu’il est utilisé pour créer un compte Media Services, à l’aide de l’API REST).


## <a name="where-is-the-refresh-token"></a>Où se trouve le jeton d’actualisation ?

Certains peuvent se demander où se trouve le jeton d’actualisation. Pourquoi ne pas utiliser un jeton d’actualisation ici ?

L’objectif d’un jeton d’actualisation n’est pas d’actualiser un jeton d’accès. En fait, il est conçu pour ignorer l’authentification utilisateur ou l’intervention utilisateur et pour toujours obtenir un jeton d’accès valide lorsqu’un jeton de version antérieure expire. Il serait peut-être plus clair de parler de « jeton de contournement de la reconnexion utilisateur ».

Si vous utilisez le flux d’octroi d’autorisation OAuth 2.0 (nom d’utilisateur et mot de passe, au nom d’un utilisateur), un jeton d’actualisation vous permet de renouveler un jeton d’accès sans demander l’intervention de l’utilisateur. Toutefois, pour le flux d’octroi d’informations d’identification client OAuth 2.0 que nous décrivons dans cet article, le client agit pour son propre compte. Vous n’avez besoin d’aucune intervention utilisateur, et le serveur d’autorisation n’a pas besoin de vous fournir un jeton d’actualisation (et ne le fera pas). Si vous déboguez la méthode **GetUrlEncodedJWT**, vous remarquerez que la réponse du point de terminaison de jeton présente un jeton d’accès, mais aucun jeton d’actualisation.

## <a name="next-steps"></a>Étapes suivantes

Commencez le [chargement de fichiers sur votre compte](media-services-dotnet-upload-files.md).

