---
title: "Didacticiel : Intégration d’Azure Active Directory à UserEcho | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et UserEcho."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: c6ea5ae5f51ac5584840a18b2d995f52297ebcf9
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Didacticiel : Intégration d’Azure Active Directory à UserEcho
L’objectif de ce didacticiel est de vous montrer comment intégrer UserEcho dans Azure Active Directory (Azure AD).

L’intégration d’UserEcho avec Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à UserEcho. 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à UserEcho par le biais de l’authentification unique (SSO) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à UserEcho, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement UserEcho pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique (SSO) Azure AD dans un environnement de test.  

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’UserEcho à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-userecho-from-the-gallery"></a>Ajouter UserEcho à partir de la galerie
Pour configurer l’intégration d’UserEcho à Azure AD, vous devez ajouter UserEcho à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter UserEcho à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **UserEcho**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)
7. Dans le volet des résultats, sélectionnez **UserEcho**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
Cette section vous indique comment configurer et tester l’authentification unique Azure AD avec UserEcho au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur UserEcho équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur UserEcho associé doit être établie.  

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans UserEcho.

Pour configurer et tester l’authentification unique Azure AD avec UserEcho, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test UserEcho](#creating-a-userecho-test-user)** pour avoir un équivalent de Britta Simon dans UserEcho lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le Portail Azure Classic et de configurer l’authentification unique dans votre application UserEcho. 

**Pour configurer l’authentification unique Azure AD avec UserEcho, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **UserEcho**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à UserEcho**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) 
  1. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application UserEcho (p. ex., *https://fabrikam.UserEcho.com/*).
  2. Cliquez sur **Suivant**.
4. Sur la page **Configurer l’authentification unique sur UserEcho** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) 
  1. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
  2. Cliquez sur **Suivant**.
5. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise UserEcho en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur votre nom d’utilisateur pour développer le menu, puis cliquez sur **Setup**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
7. Cliquez sur **Integrations**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 
8. Cliquez sur **Website**, puis sur **Single sign-on (SAML2)**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 
9. Sur la page **Single sign-on (SAML)** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) 
  1. Pour **SAML-enabled**, sélectionnez **Yes**. 
  2. Dans le portail Azure Classic, sur la page Configurer l’authentification unique sur UserEcho, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL d’authentification unique SAML**.
  3. Dans le portail Azure Classic, sur la page de boîte de dialogue Configurer l’authentification unique sur UserEcho, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **URL de déconnexion distante**. 
  4. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat X.509** .    
  5. Cliquez sur **Save**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
    
     ![Authentification unique Azure AD][10]
11. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
     ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)  
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)  
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) 
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) 
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

### <a name="create-a-userecho-test-user"></a>Créer un utilisateur de test UserEcho
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans UserEcho.

**Pour créer un utilisateur appelé Britta Simon dans UserEcho, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise UserEcho en tant qu’administrateur.
2. Dans la barre d’outils située en haut, cliquez sur votre nom d’utilisateur pour développer le menu, puis cliquez sur **Setup**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
3. Cliquez sur **Users**, pour développer la section **Users**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) 
4. Cliquez sur **Utilisateurs**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) 
5. Cliquez sur **Invite a new user**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)
6. Dans la boîte de dialogue **Invite a new user** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) 
  1. Dans la zone de texte **Name**, entrez **Britta Simon**.
  2. Dans la zone de texte **Email** , tapez l’adresse de messagerie de Britta dans le portail Azure Classic.
  3. Cliquez sur **Invite**.

Une invitation est envoyée à Britta, ce qui lui permet de commencer à utiliser UserEcho. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à UserEcho.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à UserEcho, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **UserEcho**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Quand vous cliquez sur la vignette UserEcho dans le volet d’accès, vous devez être connecté automatiquement à votre application UserEcho.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png







