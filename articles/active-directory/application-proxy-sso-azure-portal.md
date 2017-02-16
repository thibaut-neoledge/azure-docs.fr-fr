---
title: "Authentification unique auprès des applications avec le proxy d’application Azure AD | Microsoft Docs"
description: "Activez l’authentification unique pour vos applications locales publiées avec le proxy d’application Azure AD dans le portail Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fcec43ad50b9c78443ada116b9ca444605c4730
ms.openlocfilehash: fa2a2f8ee61b6edf92db8d1f92ab9aca8bc673c5

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Fournir l’authentification unique à l’aide du proxy d’application Azure AD - Version préliminaire publique

Le service Proxy d’application Azure Active Directory vous aide à améliorer la productivité en publiant des applications locales afin que les employés travaillant à distance puissent également y accéder. Dans le portail Azure, vous pouvez également configurer l’authentification unique auprès de ces applications. Maintenant, vos utilisateurs doivent seulement s’authentifier auprès d’Azure AD et ils peuvent accéder à votre application d’entreprise sans se reconnecter.

Dans cet article, nous allons utiliser l’exemple d’une application basée sur un mot de passe pour montrer comment le stockage du mot de passe fournit une expérience d’authentification unique simple. 

Vous devez avoir déjà publié et testé votre application avec le proxy d’application. Sinon, suivez les étapes de la rubrique [Publier des applications avec le proxy d’application Azure AD - Version préliminaire publique](application-proxy-publish-azure-portal.md) avant de revenir ici. 

Ou, si vous n’êtes pas familiarisé avec le proxy d’application, consultez l’article [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

## <a name="set-up-password-vaulting-for-your-application"></a>Configurer le stockage de mots de passe pour votre application

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.
3. Dans la liste, sélectionnez l’application pour laquelle vous voulez configurer l’authentification unique. Si vous avez un grand nombre d’applications, vous pouvez utiliser la zone de recherche pour les filtrer et accéder à celle qui vous intéresse.  
4. Sous la section Gérer, sélectionnez **Authentification unique**.

   ![Sélectionner l’authentification unique](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Pour le mode d’authentification unique, choisissez **Authentification par mot de passe**.
6. Pour l’URL de connexion, entrez l’URL de la page où les utilisateurs entrent leur nom d’utilisateur et leur mot de passe pour se connecter à votre application. Il doit s’agir de l’URL externe que vous avez créée quand vous avez publié l’application par le biais du proxy d’application. 

   ![Choisissez Authentification par mot de passe et entrez votre URL.](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Sélectionnez **Enregistrer**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Test de l'application

Accédez au [site Mes apps](https://myapps.microsoft.com) et sélectionnez l’application que vous venez de configurer. Connectez-vous avec vos informations d’identification pour cette application (ou celles du compte de test pour lequel vous avez configuré un accès). Une fois que vous êtes connecté, vous devez pouvoir quitter l’application et revenir sans entrer à nouveau vos informations d’identification. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autres façons d’implémenter l’[authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)



<!--HONumber=Jan17_HO2-->


