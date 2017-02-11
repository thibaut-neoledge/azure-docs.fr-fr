---
title: "Didacticiel : Intégration d’Azure Active Directory à Novatus | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LearnUpon."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 872d4dd72029d26f9b98ad1a0fd6d33da63879a6


---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Didacticiel : intégration d’Azure Active Directory à LearnUpon
L’objectif de ce didacticiel est de vous montrer comment intégrer LearnUpon dans Azure AD (Azure Active Directory).  
L’intégration de LearnUpon à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à LearnUpon.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à LearnUpon (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic. 

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables
Pour configurer l’intégration d’Azure AD à LearnUpon, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement LearnUpon pour lequel l’authentification unique est activée

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

1. Ajout de LearnUpon à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-learnupon-from-the-gallery"></a>Ajout de LearnUpon à partir de la galerie
Pour configurer l’intégration de LearnUpon à Azure AD, vous devez ajouter LearnUpon de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter LearnUpon à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, saisissez **LearnUpon**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)
7. Dans le volet des résultats, sélectionnez **LearnUpon**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique avec Azure AD avec LearnUpon en fonction d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur de LearnUpon équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur de LearnUpon associé doit être établie.  
Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** (Nom d’utilisateur) dans LearnUpon.

Pour configurer et tester l’authentification unique avec Azure AD avec LearnUpon, vous devez compléter les blocs de construction suivants :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LearnUpon](#creating-a-learnupon-test-user)** pour avoir un équivalent de Britta Simon dans LearnUpon lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application LearnUpon.

**Pour configurer l’authentification unique Azure AD avec LearnUpon, procédez comme suit :**

1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **LearnUpon**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à LearnUpon**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 
3. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 

    a. Dans la zone de texte **URL de réponse**, tapez l’URL Assertion Consumer Service à l’aide du modèle suivant : `https://\<companyname\>.learnupon.com/saml/consumer`

    b. Cliquez sur **Suivant**. 


1. Dans la page **Configurer l’authentification unique sur LearnUpon** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) 
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. Nous aurons besoin de ce certificat et des URL de métadonnées (ID d’entité, URL de connexion d’authentification unique et URL de déconnexion unique) pour configurer l’authentification unique sur LearnUpon.
   
    b. Cliquez sur **Next**.
2. Ouvrez une autre instance du navigateur et connectez-vous à LearnUpon avec un compte d’administrateur. 
3. Cliquez sur l’onglet **Paramètres** .
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 
4. Cliquez sur **Authentification unique - SAML**, puis cliquez sur **Paramètres généraux** pour configurer les paramètres SAML.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 
5. Dans la section **Paramètres généraux** procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) 
   
    a. Sélectionnez **Enabled**.
   
    b. Sous **Version**, sélectionnez **2.0**.
   
    c. Sous **Skip conditions** (Ignorer les conditions), sélectionnez **Non**.
   
    d. Dans la zone de texte **SAML Token POST param name** (Nom de paramètre POST du jeton SAML), entrez le nom du paramètre de publication de demande correspondant à l’URL de consommateur SAML indiquée ci-dessus, qui contient l’assertion SAML à vérifier et à authentifier. Par exemple, **SAMLResponse**.
   
    e. Dans la zone de texte **Format de l’identificateur du nom**, entrez la valeur indiquant à quel emplacement dans votre assertion SAML réside l’identificateur de l’utilisateur (adresse e-mail), par exemple, **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
   
    f. Dans la zone de texte **Identify Provider Location** (Identifier l’emplacement du fournisseur), entrez la valeur indiquant l’emplacement vers lequel sont envoyés les utilisateurs lorsqu’ils cliquent sur l’icône de téléchargement à partir de l’écran de connexion au portail Azure Classic.
   
    g. Dans le portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de déconnexion**.
   
    h. Cliquez sur **Manage finger prints**(Gérer les empreintes), puis téléchargez l’empreinte du certificat téléchargé. 
6. Cliquez sur **Paramètres utilisateur**, puis procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) 
   
    a. Dans la zone de texte **First Name Identifier Format** (Format de l’identificateur du nom), entrez la valeur indiquant à quel emplacement dans votre assertion SAML réside le prénom des utilisateurs, par exemple, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/    givenname**.
   
    b. Dans la zone de texte **Last Name Identifier Format** (Format de l’identificateur du nom), entrez la valeur indiquant à quel emplacement dans votre assertion SAML réside le nom des utilisateurs, par exemple, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/    surname**.
7. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Suivant**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-learnupon-test-user"></a>Création d’un utilisateur de test LearnUpon
L’objectif de cette section est de créer l’utilisateur Britta Simon dans LearnUpon. LearnUpon prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé au moment d’une tentative d’accès à LearnUpon s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique LearnUpon.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à LearnUpon.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LearnUpon, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **LearnUpon**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Quand vous cliquez sur la vignette LearnUpon dans le volet d’accès, vous devez être connecté automatiquement à votre application LearnUpon.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


