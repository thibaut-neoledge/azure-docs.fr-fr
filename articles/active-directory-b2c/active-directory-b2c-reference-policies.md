---
title: "Azure Active Directory B2C : infrastructure de stratégie extensible | Microsoft Docs"
description: "Une rubrique sur l&quot;infrastructure de stratégie extensible d&quot;Azure Active Directory B2C et la création de différents types de stratégies"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 7ad7dce6fc7025a6a6e7935d74b3b71a7bfee097
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active Directory B2C : infrastructure de stratégie extensible
## <a name="the-basics"></a>Concepts de base
L’infrastructure de stratégie extensible d’Azure Active Directory (Azure AD) B2C est le pilier central du service. Les stratégies décrivent entièrement les expériences relatives à l’identité, telles que l’inscription, la connexion ou la modification de profil. Par exemple, une stratégie d'inscription vous permet de contrôler les comportements en configurant les paramètres suivants :

* Types de comptes (comptes sociaux, tels que Facebook et comptes locaux, tels que l’adresse de messagerie) que les clients peuvent utiliser pour s’inscrire à l’application.
* Attributs (par exemple, prénom, code postal et pointure) à recueillir auprès du client lors de l’inscription.
* Utilisation de Multi-Factor Authentication.
* Aspect de toutes les pages d'inscription.
* Informations (qui se manifestent en tant que revendications dans un jeton) que l’application reçoit lorsque l’exécution de la stratégie est terminée.

Vous pouvez créer plusieurs stratégies de types différents dans votre client et les utiliser dans vos applications en fonction des besoins. Les stratégies peuvent être réutilisées entre les différentes applications. Ceci permet aux développeurs de définir et de modifier les expériences relatives à l’identité du client avec peu ou pas de modification du code.

Les stratégies sont disponibles à l'utilisation par le biais d'une interface développeur simple. Votre application déclenche une stratégie avec une requête d'authentification HTTP standard (en transmettant un paramètre de stratégie dans la requête) et reçoit un jeton personnalisé en tant que réponse. Par exemple, la seule différence entre les requêtes invoquant une stratégie d’inscription et celles invoquant une stratégie de connexion réside dans le nom de la stratégie utilisé dans le paramètre de chaîne de requête « p » :

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Pour plus d’informations sur l’infrastructure de stratégie, consultez ce [billet de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Création d’une stratégie d’inscription
Pour activer l’inscription dans votre application, vous devez créer une stratégie d’inscription. Cette stratégie décrit les expériences des clients lors de l’inscription et le contenu des jetons que l’application reçoit en cas d’inscription réussie.

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies d'inscription**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Le **Nom** détermine le nom de la stratégie d'inscription utilisé par votre application. Par exemple, entrez « SiUp ».
5. Cliquez sur **Fournisseurs d’identité** et sélectionnez « Inscription par le biais d’une adresse e-mail ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.
6. Cliquez sur **Attributs d’inscription**. Ici, vous choisissez les attributs que vous souhaitez collecter auprès du client au cours de l’inscription. Par exemple, sélectionnez « Pays/région », « Nom d'affichage » et « Code postal ». Cliquez sur **OK**.
7. Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience d’inscription réussie. Par exemple, sélectionnez « Nom d'affichage », « Fournisseur d'identité », « Code postal », « Nouvel utilisateur » et « ID d'objet de l'utilisateur ».
8. Cliquez sur **Create**. Notez que la stratégie créée s’affiche sous la forme « **B2C_1_SiUp** » (le fragment **B2C\_1\_** est automatiquement ajouté) dans le panneau **Stratégies d’inscription**.
9. Ouvrez la stratégie en cliquant sur « **B2C_1_SiUp** ».
10. Sélectionnez « Contoso B2C app » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la liste déroulante **URL de réponse/URI de redirection**.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous inscrire à votre application.
    
    > [!NOTE]
    > La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>Création d’une stratégie de connexion
Pour activer la connexion à votre application, vous devez créer une stratégie de connexion. Cette stratégie décrit les expériences des clients lors de la connexion et le contenu des jetons que l’application reçoit en cas de connexion réussie.

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies d’authentification**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Le **Nom** détermine le nom de la stratégie de connexion utilisée par votre application. Par exemple, entrez « SiIn ».
5. Cliquez sur **Fournisseurs d’identité** et sélectionnez « Inscription par le biais du compte local ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.
6. Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience de connexion réussie. Par exemple, sélectionnez « Nom d’affichage », « Fournisseur d’identité », « Code postal » et « ID d’objet de l’utilisateur ». Cliquez sur **OK**.
7. Cliquez sur **Create**. Notez que la stratégie créée s’affiche sous la forme « **B2C_1_SiIn** » (le fragment **B2C\_1\_** est automatiquement ajouté) dans le panneau **Stratégies de connexion**.
8. Ouvrez la stratégie en cliquant sur « **B2C_1_SiIn** ».
9. Sélectionnez « Contoso B2C app » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la liste déroulante **URL de réponse/URI de redirection**.
10. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous connecter à votre application.
    
    > [!NOTE]
    > La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>Création d’une stratégie d’inscription ou de connexion
Cette stratégie gère les expériences d’inscription et de connexion des utilisateurs avec une configuration unique. Les utilisateurs sont dirigés vers le chemin d’accès correct (inscription ou connexion) en fonction du contexte. Cet article décrit également le contenu des jetons que l’application recevra en cas d’inscription ou de connexion réussie.  Un exemple de code pour la stratégie d’inscription ou de connexion est [disponible ici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies d’inscription ou de connexion**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Le **Nom** détermine le nom de la stratégie d'inscription utilisé par votre application. Par exemple, entrez « SiUpIn ».
5. Cliquez sur **Fournisseurs d’identité** et sélectionnez « Inscription par le biais d’une adresse e-mail ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.
6. Cliquez sur **Attributs d’inscription**. Ici, vous choisissez les attributs que vous souhaitez collecter auprès du client au cours de l’inscription. Par exemple, sélectionnez « Pays/région », « Nom d'affichage » et « Code postal ». Cliquez sur **OK**.
7. Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience de d’inscription ou de connexion réussie. Par exemple, sélectionnez « Nom d'affichage », « Fournisseur d'identité », « Code postal », « Nouvel utilisateur » et « ID d'objet de l'utilisateur ».
8. Cliquez sur **Create**. Notez que la stratégie créée s’affiche sous la forme « **B2C_1_SiUpIn** » (le fragment **B2C\_1\_** est ajouté automatiquement) dans le panneau **Stratégies d’inscription ou de connexion**.
9. Ouvrez la stratégie en cliquant sur « **B2C_1_SiUpIn** ».
10. Sélectionnez « Contoso B2C app » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la liste déroulante **URL de réponse/URI de redirection**.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous inscrire ou à vous connecter à votre application.
    
    > [!NOTE]
    > La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil
Pour activer la modification de profil dans votre application, vous devez créer une stratégie de modification de profil. Cette stratégie décrit les expériences des clients lors de la modification du profil et le contenu des jetons que l'application reçoit en cas d'opération réussie.

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies de modification du profil**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Le **Nom** détermine le nom de la stratégie de modification de profil utilisé par votre application. Par exemple, entrez « SiPe ».
5. Cliquez sur **Fournisseurs d’identité** et sélectionnez « Inscription par le biais du compte local ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.
6. Cliquez sur **Attributs de profil**. Ici, vous choisissez les attributs que le client peut afficher et modifier. Par exemple, sélectionnez « Pays/région », « Nom d’affichage » et « Code postal ». Cliquez sur **OK**.
7. Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience de modification de profil réussie. Par exemple, sélectionnez « Nom d'affichage » et « Code postal ».
8. Cliquez sur **Create**. Notez que la stratégie créée s’affiche sous la forme « **B2C_1_SiPe** » (le fragment **B2C\_1\_** est ajouté automatiquement) dans le panneau **Stratégies de modification du profil**.
9. Ouvrez la stratégie en cliquant sur « **B2C_1_SiPe** ».
10. Sélectionnez « Contoso B2C app » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la liste déroulante **URL de réponse/URI de redirection**.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à modifier un profil dans votre application.
    
    > [!NOTE]
    > La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe
Pour activer la réinitialisation affinée du mot de passe sur votre application, vous devez créer une stratégie de réinitialisation du mot de passe. Notez que l’option de réinitialisation du mot de passe au niveau du client spécifiée [ici](active-directory-b2c-reference-sspr.md) est toujours applicable pour les stratégies de connexion. Cette stratégie décrit les expériences des clients lors de la réinitialisation du mot de passe et le contenu des jetons que l’application reçoit en cas d’opération réussie.

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies de réinitialisation du mot de passe**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Le **Nom** détermine le nom de la stratégie de réinitialisation du mot de passe utilisé par votre application. Par exemple, entrez « SSPR ».
5. Cliquez sur **Fournisseurs d’identité** et sélectionnez « Réinitialiser le mot de passe à l’aide de l’adresse de messagerie ». Cliquez sur **OK**.
6. Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience de réinitialisation du mot de passe réussie. Par exemple, sélectionnez « ID objet utilisateur ».
7. Cliquez sur **Create**. Notez que la stratégie créée s’affiche sous la forme « **B2C_1_SSPR** » (le fragment **B2C\_1\_** est ajouté automatiquement) dans le panneau **Stratégies de réinitialisation du mot de passe**.
8. Ouvrez la stratégie en cliquant sur « **B2C_1_SSPR** ».
9. Sélectionnez « Contoso B2C app » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la liste déroulante **URL de réponse/URI de redirection**.
10. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à réinitialiser un mot de passe dans votre application.
    
    > [!NOTE]
    > La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Comment lier une stratégie d’inscription ou de connexion à une stratégie de réinitialisation de mot de passe ?
Quand vous créez une stratégie d’inscription ou de connexion (avec des comptes locaux), le consommateur voit un lien « Mot de passe oublié ? » sur la première page de l’expérience. Cliquer sur ce lien ne déclenche pas automatiquement une stratégie de réinitialisation de mot de passe. Au lieu de cela, un code d’erreur spécifique `AADB2C90118` est retourné à votre application. Votre application doit gérer ceci et appeler une stratégie de réinitialisation de mot de passe spécifique. Vous pouvez trouver un exemple illustrant cette approche de liaison de stratégies [ici](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Configuration du jeton, de la session et de l’authentification unique](active-directory-b2c-token-session-sso.md).
* [Désactiver la vérification par courrier électronique lors de l’inscription du consommateur](active-directory-b2c-reference-disable-ev.md)


