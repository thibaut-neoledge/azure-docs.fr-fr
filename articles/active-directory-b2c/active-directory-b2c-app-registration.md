---
title: "Azure Active Directory B2C : inscription d’application | Microsoft Docs"
description: "Inscription de votre application auprès d’Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C : inscription de votre application

> [!IMPORTANT]
> Les applications créées dans le panneau Azure AD B2C dans le portail Azure doivent être gérées à partir du même emplacement. Si vous modifiez des applications B2C à l’aide de PowerShell ou d’un autre portail, ces dernières ne sont plus prises en charge et ne fonctionneront pas avec Azure AD B2C. Pour plus d’informations, consultez [la section ci-dessous](#faulted-apps).
>

## <a name="prerequisite"></a>Configuration requise

Pour générer une application acceptant l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un client Azure Active Directory B2C. Obtenez votre propre client en suivant la procédure décrite dans [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md). Après avoir suivi toutes les étapes de cet article, le panneau de fonctionnalités B2C doit être épinglé sur votre Tableau d’accueil.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Accéder au panneau de fonctionnalités B2C

Si le panneau de fonctionnalités B2C est épinglé à votre Tableau d’accueil, vous pouvez le voir dès que vous vous connectez au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur général du client B2C.

Vous pouvez également accéder au panneau en cliquant sur **Plus de services**, puis en recherchant **Azure AD B2C** dans le panneau de navigation à gauche du [Portail Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Vous devez être administrateur général du client B2C pour accéder au volet des fonctionnalités B2C. L’administrateur général ou l’utilisateur de tout autre client ne pourra pas y accéder.  Vous pouvez basculer vers votre client B2C en utilisant le sélecteur de client dans le coin supérieur droit du portail Azure.
>
>

## <a name="register-a-web-application"></a>Inscrire une application web

1. Dans le panneau de fonctionnalités B2C du Portail Azure, cliquez sur **Applications**.
1. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
1. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, entrez « Application B2C Contoso ».
1. Activez/désactivez le commutateur **Include web app / web API** (Inclure l’application web/l’API web) pour le définir sur **Oui**.
1. Entrez une valeur [adéquate](#limitations) pour les **URL de réponse**, qui sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Par exemple, entrez : `https://localhost:44316/`.
1. Cliquez sur **Créer** pour inscrire votre application.
1. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code.
1. Si votre application web appellera également une API web sécurisée par Azure AD B2C, vous souhaiterez :
    1. Créer un **Clé secrète d’application** en accédant au panneau **Clés** panneau et en cliquant sur le bouton **Générer la clé**.
    1. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
>

## <a name="register-a-web-api"></a>Inscrire une API web

1. Dans le panneau de fonctionnalités B2C du Portail Azure, cliquez sur **Applications**.
1. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
1. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, vous pouvez entrer « API B2C Contoso ».
1. Activez/désactivez le commutateur **Include web app / web API** (Inclure l’application web/l’API web) pour le définir sur **Oui**.
1. Entrez une valeur [adéquate](#choosing-a-web-app/api-reply-url) pour les **URL de réponse**, qui sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Par exemple, entrez : `https://localhost:44316/`.
1. Entrez un **URI ID d’application**. Il s’agit de l’identificateur utilisé pour votre API web. Par exemple, entrez « notes ». Il génère l’identificateur URI complet en dessous.
1. Cliquez sur **Créer** pour inscrire votre application.
1. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code.
1. Cliquez sur **Étendues publiées**. C’est ici que vous définissez les autorisations (étendues) qui peuvent être accordées à d’autres applications.
1. Ajoutez des étendues si nécessaire. Par défaut, l’étendue « user_impersonation » est définie. Cela permet à d’autres applications d’accéder à cette API pour le compte de l’utilisateur connecté. Vous pouvez la supprimer si vous le souhaitez.
1. Cliquez sur **Save**.

## <a name="register-a-mobilenative-application"></a>Inscrire une application mobile/native

1. Dans le panneau de fonctionnalités B2C du Portail Azure, cliquez sur **Applications**.
1. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
1. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, entrez « Application B2C Contoso ».
1. Activez/désactivez l’interrupteur **Include native client** (Inclure le client natif) pour le définir sur **Oui**.
1. Entrez un **URI de redirection** avec un schéma personnalisé. Par exemple, com.onmicrosoft.contoso.appname://redirect/path. Prenez soin de choisir un [URI de redirection approprié](#choosing-a-native-application-redirect-uri) et de ne pas y inclure de caractères spéciaux tels que des traits de soulignement.
1. Cliquez sur **Enregistrer** pour inscrire votre application.
1. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code.
1. Si votre application native appellera également une API web sécurisée par Azure AD B2C, vous souhaiterez :
    1. Créer un **Clé secrète d’application** en accédant au panneau **Clés** panneau et en cliquant sur le bouton **Générer la clé**.
    1. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
>

## <a name="limitations"></a>Limitations

### <a name="choosing-a-web-appapi-reply-url"></a>Choix d’une URL de réponse d’API/application web

Pour l’instant, les applications qui sont inscrites auprès d’Azure AD B2C sont limitées à un ensemble restreint de valeurs d’URL de réponse. L’URL de réponse pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URL de réponse doivent partager un même domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web comportant l’une des URL de réponse suivantes :

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URL de réponse existante au nom DNS de l’URL de réponse que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :

* Le nom DNS complet de la nouvelle URL de réponse ne correspond pas au nom DNS de l’URL de réponse existante.
* Le nom DNS complet de la nouvelle URL de réponse n’est pas un sous-domaine de l’URL de réponse existante.

Par exemple, si l’application a cette URL de réponse :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous voulez disposer d’une application avec login-east.contoso.com et login-west.contoso.com comme URL de réponse, vous devez ajouter ces URL de réponse dans l’ordre suivant :

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Vous pouvez ajouter les deux derniers car il s’agit de sous-domaines de la première URL de réponse, contoso.com.

### <a name="choosing-a-native-application-redirect-uri"></a>Choix d’un URI de redirection d’application native

Il existe deux points importants à prendre en considération lors du choix d’un URI de redirection pour les applications mobiles/natives :

* **Unique** : le schéma de l’URI de redirection doit être unique pour chaque application. Dans notre exemple (com.onmicrosoft.contoso.appname://redirect/path), nous utilisons com.onmicrosoft.contoso.appname en tant que schéma. Nous vous recommandons de suivre ce modèle. Si deux applications partagent le même schéma, l’utilisateur verra une boîte de dialogue « Choix d’une application ». Si l’utilisateur effectue un choix incorrect, la connexion échoue.
* **Complet** : l’URI de redirection doit comporter un schéma et un chemin d’accès. Le chemin d’accès doit contenir au moins une barre oblique après le domaine (par exemple, //contoso/ fonctionnera, tandis que //contoso échouera).

Vérifiez que l’URI de redirection ne comporte aucun caractère spécial, tel que des traits de soulignement.

### <a name="faulted-apps"></a>Applications ayant généré une erreur

Les applications B2C ne doivent PAS être modifiées :

* Sur les autres portails de gestion des applications tels que le [Portail Azure Classic](https://manage.windowsazure.com/) et le [Portail d’inscription des applications](https://apps.dev.microsoft.com/)
* À l’aide de l’API Graph ou de PowerShell

Si vous modifiez l’application B2C comme décrit ci-dessus, puis que vous tentez de la modifier de nouveau dans le panneau des fonctionnalités d’Azure AD B2C sur le Portail Azure, l’application générera une erreur et ne sera plus utilisable avec Azure AD B2C. Vous devrez supprimer l’application, puis la recréer.

Pour supprimer l’application, accédez au [Portail d’inscription des applications](https://apps.dev.microsoft.com/), puis supprimez l’application à cet emplacement. Pour que l’application soit visible, vous devez en être le propriétaire (et non simplement un administrateur du locataire).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une application inscrite auprès d’Azure AD B2C, vous pouvez suivre l’un de [nos didacticiels de démarrage rapide](active-directory-b2c-overview.md#get-started) afin de devenir opérationnel.

