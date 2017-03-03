---
title: "Didacticiel : Intégration d’Azure Active Directory avec Learning at Work | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Learning at Work."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d607174-bea1-4f40-8233-54cabe02c66a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6144dd5309128d0069d15b8f6194ad920fb29f6a
ms.openlocfilehash: f8be19e8c43368cffec7249f7a07217c3252b06a
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-learning-at-work"></a>Didacticiel : Intégration d’Azure Active Directory à Learning at Work
Dans ce didacticiel, vous allez apprendre à intégrer Learning at Work à Azure Active Directory (Azure AD).

L’intégration de Learning at Work dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Learning at Work
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Learning at Work via l’authentification unique (SSO) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Learning at Work, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Learning at Work (Saba Cloud) pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Learning at Work à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-learning-at-work-from-the-gallery"></a>Ajouter Learning at Work à partir de la galerie
Pour configurer l’intégration de Learning at Work avec Azure AD, vous devez ajouter Learning at Work à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Learning at Work à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **Learning at Work**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_01.png)
7. Dans le volet de résultats, sélectionnez **Learning at Work**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Learning at Work avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Learning at Work équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Learning at Work associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Learning at Work.

Pour configurer et tester l'authentification unique Azure AD avec Learning at Work, vous devez suivre les blocs élémentaires suivants :

1. **[Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Learning at Work](#creating-a-predictix-price-reporting-test-user)** pour avoir un équivalent de Britta Simon dans Learning at Work lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Learning at Work.

**Pour configurer l’authentification unique Azure AD avec Learning at Work, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration d’applications **Learning at Work**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Learning at Work**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_04.png) 
   
    1. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application ICIMS, au format suivant : `https://\<company name\>.sabacloud.com/Saba/Web/<company code>`
    2. Dans la zone de texte **Identificateur**, tapez l’URL au format suivant : `https://<company name>.sabacloud.com/Saba/SAML/sso/alias/<company name>`
    3. Cliquez sur **Suivant**.
4. Dans la page **Configurer l’authentification unique sur Learning at Work** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_05.png)
   
    1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
    2. Cliquez sur **Suivant**.
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Learning at Work (Saba Cloud) et fournissez-lui les éléments suivants :  
     * Les métadonnées téléchargées
     * **L’URL de l’émetteur**
     * **L’URL d’authentification unique SAML**
     * L **’URL du service d’authentification unique**
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_05.png) 
  
    1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
    2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
    3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_06.png) 
   1. Dans la zone de texte **First Name**, tapez **Britta**.  
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_08.png) 
    1. Notez la valeur du **Nouveau mot de passe**.
    2. Cliquez sur **Terminé**.   

### <a name="create-an-learning-at-work-test-user"></a>Créer un utilisateur de test Learning at Work
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Learning at Work. Veuillez travailler avec l’équipe de support technique de Learning at Work pour ajouter les utilisateurs dans la plate-forme Learning at Work.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Learning at Work.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Learning at Work, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Learning at Work**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Learning at Work dans le volet d’accès, vous devez être connecté automatiquement à votre application Learning at Work.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_205.png

