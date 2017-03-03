---
title: "Didacticiel : Intégration d’Azure Active Directory à Keylight | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Keylight."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: f58c0967890ee99c574957f0cdfe1bb412f7f9e8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Didacticiel : Intégration d’Azure Active Directory à Keylight
Dans ce didacticiel, vous allez apprendre à intégrer Keylight à Azure AD (Azure Active Directory).

L’intégration de Keylight à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Keylight
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Keylight via l’authentification unique (SSO) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Configuration requise
Pour configurer l’intégration d’Azure AD à Keylight, vous avez besoin des éléments suivants :

* Un abonnement Azure
* Un abonnement Keylight pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Keylight à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-keylight-from-the-gallery"></a>Ajouter Keylight à partir de la galerie
Pour configurer l’intégration de Keylight à Azure AD, vous devez ajouter Keylight, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Keylight à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Keylight**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)
7. Dans le volet de résultats, sélectionnez **Keylight**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

## <a name="configure-and-testing-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Keylight avec un utilisateur de test appelé « Britta Simon ».

Pour configurer et tester l’authentification unique Azure AD avec Keylight, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Keylight](#creating-a-keylight-test-user)** pour avoir un équivalent de Britta Simon dans Keylight lié à sa représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Keylight.

**Pour configurer l’authentification unique Azure AD avec Keylight, procédez comme suit :**

1. Dans le portail Azure Classic, cliquez dans la page d’intégration d’application **Keylight** sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Keylight**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 

    * Dans la zone de texte URL de connexion, entrez l’URL utilisée par les utilisateurs pour se connecter à votre application Keylight au format suivant : **« https://\<nom_entreprise\>.keylightgrc.com/Login.aspx?saml=1 »**.

4. Dans la page **Configurer l’authentification unique sur Keylight** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 
   
    1. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
    2. Cliquez sur **Suivant**.
5. Pour activer l’authentification unique dans Keylight, procédez comme suit :
   
    1. Connectez-vous à votre compte Keylight en tant qu’administrateur.
    2. Dans le menu situé en haut, cliquez sur **Person** (Personne), puis sélectionnez **Keylight Setup** (Installation de Keylight).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/401.png) 
    3. Dans l’arborescence sur la gauche, cliquez sur **SAML**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/402.png) 
    4. Dans la boîte de dialogue **SAML Settings** (Paramètres SAML), cliquez sur **Edit** (Modifier).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/404.png) 
6. Dans la page de boîte de dialogue **Edit SAML Settings** (Modifier les paramètres SAML), procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    1. Affectez à **SAML authentication** (Authentication SAML) la valeur **Active**.
    2. Dans le portail classique Azure AD, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité).
    3. Dans le portail classique Azure AD, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité).
    4. Cliquez sur **Choose File** (Choisir un fichier) pour sélectionner votre certificat Keylight téléchargé, puis sur **Ouvrir** pour charger le certificat.
    5. Affectez à **SAML User Id location** (Emplacement de l’ID utilisateur SAML) la valeur **NameIdentifier element of the subject statement** (Élément NameIdentifier de l’instruction Subject).
    6. Indiquez la valeur **Keylight Service Provider (Fournisseur de service Keylight) au format suivant : **https://&lt;Nom société&gt;.keylightgrc.com**.
    7. Spécifiez les paramètres suivants :
     * Affectez à **Auto-provision users** (Configuration automatique des utilisateurs) la valeur **Active**.
     * Affectez à **Auto-provision account type** (Configuration automatique du type de compte) la valeur **Full User** (Utilisateur global).
     * Pour **Auto-provision security role** (Configuration automatique du rôle de sécurité), sélectionnez **Standard User with SAML** (Utilisateur standard avec SAML).
     * Pour **Auto-provision security config** (Configuration automatique des paramètres de sécurité), sélectionnez **Standard User Configuration** (Configuration utilisateur standard).
    8. Entrez les informations suivantes :    
     * Dans la zone de texte Email, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
     * Dans la zone de texte **First Name attribute**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
     * Dans la zone de texte **Last Name attribute**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9. Cliquez sur **Save**.

7. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

* Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 
   
   1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 
   
   1. Dans la zone de texte **First Name**, tapez **Britta**.    
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 
   
    1. Notez la valeur du **Nouveau mot de passe**.
    2. Cliquez sur **Terminé**.   

### <a name="create-a-keylight-test-user"></a>Créer un utilisateur de test Keylight
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Keylight. Keylight prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors de l’accès à Keylight si l’utilisateur n’existe pas encore. 

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’équipe de support Keylight. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Keylight.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Keylight, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Keylight**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Keylight dans le volet d’accès, vous devez être connecté automatiquement à votre application Keylight.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

