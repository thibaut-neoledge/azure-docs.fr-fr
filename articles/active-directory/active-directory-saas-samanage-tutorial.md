---
title: "Didacticiel : Intégration d’Azure Active Directory avec Samanage | Microsoft Docs"
description: "Apprenez à utiliser Samanage avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da49f1d9f8aa5fbbaf6e16f1db03a16f80a2bc92


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Didacticiel : Intégration d’Azure Active Directory à Samanage
L’objectif de ce didacticiel est de vous montrer comment intégrer Samanage dans Azure Active Directory (Azure AD).

L’intégration de Samanage à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Samanage.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Samanage (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Samanage, vous avez besoin des éléments suivants :

* Un abonnement Azure valide
* Un locataire Samanage

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Samanage à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-samanage-from-the-gallery"></a>Ajout de Samanage à partir de la galerie
Pour configurer l’intégration de Samanage à Azure AD, vous devez ajouter Samanage à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Samanage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Samanage**.
   
   ![Galerie d’applications](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Application gallery")
7. Dans le volet des résultats, sélectionnez **Samanage**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Samanage au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Samanage équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Samanage associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Samanage.

Pour configurer et tester l’authentification unique Azure AD avec Samanage, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Samanage](#creating-a-Samanage-test-user)** pour avoir un équivalent de Britta Simon dans Samanage qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Samanage.

**Pour configurer l’authentification unique Azure AD avec Samanage, procédez comme suit :**

1. Dans la page d’intégration d’application **Samanage** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Samanage**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Authentification unique Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-On")
3. Sur la page Configurer les paramètres d’application, procédez comme suit :
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configure App URL")
   
   a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Company Name>.samanage.com/saml_login/<Company Name>`.
   
   b. click **Suivant**
   
   > [!NOTE]
   > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion réelle. Pour obtenir ces valeurs, reportez-vous à l’étape 8.c pour plus de détails ou contactez Samanage.
   > 
   > 
4. Dans la page **Configurer l’authentification unique sur Samanage**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Samanage en tant qu’administrateur.
6. Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation de gauche.
   
   ![Tableau de bord](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. Cliquez sur **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. Accédez à la section **Login using SAML** (Connexion avec SAML), puis procédez comme suit :
   
   ![Login using SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
   
   a.  Cliquez sur **Enable Single Sign-On with SAML**(Activer l’authentification unique avec SAML).
   
   b.  Dans la zone de texte **Identity Provider URL** (URL du fournisseur d’identité), copiez la valeur de **ID de fournisseur d’identité** indiquée dans l’Assistant Configuration de l’application Azure AD.    
   
   c.  Vérifiez que l’**URL de connexion** correspond à l’**URL d’authentification** indiquée à l’étape 3.
   
   d.  Dans la zone de texte **Logout URL** (URL de déconnexion), copiez la valeur de l’**URL de déconnexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
   e. Dans la zone de texte **SAML Issuer** (Émetteur SAML), saisissez l’URI d’ID d’application défini dans votre fournisseur d’identité.
   
   f.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Paste your Identity Provider x.509 Certificate below** (Collez le certificat X.509 de votre fournisseur d’identité ci-dessous).
   
   g.  Cliquez sur **Create users if they do not exist in Samanage**(Créer des utilisateurs s’ils n’existent pas dans Samanage).
   
   h.  Cliquez sur **Mettre à jour**.
9. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configure Single Sign-On")
10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-samanage-test-user"></a>Création d’un utilisateur de test Samanage
Pour se connecter à Samanage, les utilisateurs d’Azure AD doivent être approvisionnés dans Samanage. Dans le cas de Samanage, l’approvisionnement est une tâche manuelle.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site d’entreprise Samanage en tant qu’administrateur.
2. Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation gauche.
   
   ![Paramétrage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")
3. Cliquez sur l’onglet **Users** .
   
   ![Utilisateurs](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")
4. Cliquez sur **New User**.
   
   ![Nouvel utilisateur](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. Entrez le **nom** et l’**adresse de messagerie** du compte Azure AD que vous souhaitez approvisionner, puis cliquez sur **Create user**.
   
   ![Créer un utilisateur](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   > [!NOTE]
   > Le détenteur du compte AAD recevra un message électronique et suivra un lien pour confirmer le compte avant qu’il ne soit activé. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Samanage pour approvisionner des comptes d’utilisateur Azure Active Directory.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Samanage.

![Affecter des utilisateurs](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assign users")

**Pour attribuer Britta Simon à Samanage, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assign users")
2. Dans la liste des applications, sélectionnez **Samanage**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assign users")
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Samanage dans le volet d’accès, vous devez être connecté automatiquement à votre application Samanage.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


