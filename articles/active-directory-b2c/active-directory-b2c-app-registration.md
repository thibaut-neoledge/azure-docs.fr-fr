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
ms.date: 3/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 414c684c78314a92bbbe12e6164e2b10fb682b0f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C : inscription de votre application

> [!IMPORTANT]
> Les applications créées dans le panneau Azure AD B2C dans le portail Azure doivent être gérées à partir du même emplacement. Si vous modifiez les applications B2C à l’aide de PowerShell ou d’un autre portail, elles ne sont plus prises en charge et ne fonctionneront probablement pas avec Azure AD B2C.
> 
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
2. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
3. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, entrez « Application B2C Contoso ».
4. Activez/désactivez le commutateur **Include web app / web API** (Inclure l’application web/l’API web) pour le définir sur **Oui**. Les **URL de réponse** sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Par exemple, entrez : `https://localhost:44316/`.
5. Cliquez sur **Créer** pour inscrire votre application.
6. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code. 
7. Si votre application web appellera également une API web sécurisée par Azure AD B2C, vous souhaiterez :
   1. Créer un **Clé secrète d’application** en accédant au panneau **Clés** panneau et en cliquant sur le bouton **Générer la clé**.
   2. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
> 
   

## <a name="register-a-web-api"></a>Inscrire une API web
1. Dans le panneau de fonctionnalités B2C du Portail Azure, cliquez sur **Applications**.
2. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
3. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, vous pouvez entrer « API B2C Contoso ».
4. Activez/désactivez le commutateur **Include web app / web API** (Inclure l’application web/l’API web) pour le définir sur **Oui**. Les **URL de réponse** sont des points de terminaison auxquels Azure AD B2C renvoie les jetons demandés par votre application. Par exemple, entrez : `https://localhost:44316/`.
5. Entrez un **URI ID d’application**. Il s’agit de l’identificateur utilisé pour votre API web. Par exemple, entrez « notes ». Il génère l’identificateur URI complet en dessous. 
6. Cliquez sur **Créer** pour inscrire votre application.
7. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code.
8. Cliquez sur **Étendues publiées**. C’est ici que vous définissez les autorisations (étendues) qui peuvent être accordées à d’autres applications.
9. Ajoutez des étendues si nécessaire. Par défaut, l’étendue « user_impersonation » est définie. Cela permet à d’autres applications d’accéder à cette API pour le compte de l’utilisateur connecté. Vous pouvez la supprimer si vous le souhaitez. 
10. Cliquez sur **Save**.

## <a name="register-a-mobilenative-application"></a>Inscrire une application mobile/native
1. Dans le panneau de fonctionnalités B2C du Portail Azure, cliquez sur **Applications**.
2. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
3. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, entrez « Application B2C Contoso ».
4. Activez/désactivez l’interrupteur **Include native client** (Inclure le client natif) pour le définir sur **Oui**.
5. Entrez un **URI de redirection** avec un schéma personnalisé. Par exemple, com.onmicrosoft.contoso.appname://redirect/path. Veillez à choisir un [URI de redirection adapté](#choosing-a-redirect-uri).
6. Cliquez sur **Enregistrer** pour inscrire votre application.
7. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code.
8. Si votre application native appellera également une API web sécurisée par Azure AD B2C, vous souhaiterez :
   1. Créer un **Clé secrète d’application** en accédant au panneau **Clés** panneau et en cliquant sur le bouton **Générer la clé**.
   2. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
> 

### <a name="choosing-a-redirect-uri"></a>Sélection d’un URI de redirection
Il existe deux points importants à prendre en considération lors du choix d’un URI de redirection pour les applications mobiles/natives : 
* **Unique** : le schéma de l’URI de redirection doit être unique pour chaque application. Dans notre exemple (com.onmicrosoft.contoso.appname://redirect/path), nous utilisons com.onmicrosoft.contoso.appname en tant que schéma. Nous vous recommandons de suivre ce modèle. Si deux applications partagent le même schéma, l’utilisateur verra une boîte de dialogue « Choix d’une application ». Si l’utilisateur effectue un choix incorrect, la connexion échoue. 
* **Complet** : l’URI de redirection doit comporter un schéma et un chemin d’accès. Le chemin d’accès doit contenir au moins une barre oblique après le domaine (par exemple, //contoso/ fonctionnera, tandis que //contoso échouera). 

## <a name="build-an-application"></a>Générer une application
À présent que vous avez une application inscrite auprès d’Azure AD B2C, vous pouvez suivre l’un de [nos didacticiels de démarrage rapide](active-directory-b2c-overview.md#get-started) afin de devenir opérationnel.


