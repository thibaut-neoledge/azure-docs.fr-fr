---
title: "Didacticiel : Intégration d’Azure Active Directory à Alcumus Info Exchange | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Alcumus Info Exchange."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d26034b8-f0d5-4f65-aa56-0fc168ceec8c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 4c22b52d1c86fefbccd452967c6250fdecaac6dd


---
# <a name="tutorial-azure-active-directory-integration-with-alcumus-info-exchange"></a>Didacticiel : Intégration d’Azure Active Directory avec Alcumus Info Exchange
L’objectif de ce didacticiel est de vous montrer comment intégrer Alcumus dans Azure Active Directory (Azure AD).  
L’intégration d’Alcumus Info Exchange dans Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à Alcumus Info Exchange. 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Alcumus Info Exchange (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Alcumus Info Exchange, vous avez besoin des éléments suivants :

* Un abonnement [Azure AD](https://azure.microsoft.com/)
* Un abonnement [Alcumus Info Exchange](http://www.alcumusgroup.com/) pour lequel l’authentification unique est activée

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

1. Ajout d’Alcumus info Exchange à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-alcumus-info-exchange-from-the-gallery"></a>Ajout d’Alcumus info Exchange à partir de la galerie
Pour configurer l’intégration d’Alcumus Info Exchange dans Azure AD, vous devez ajouter Alcumus Info Exchange, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Alcumus Info Exchange à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Alcumus Info Exchange**.
   
    ![Applications][5]
7. Dans le volet des résultats, sélectionnez **Alcumus Info Exchange**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Applications][400]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Alcumus Info Exchange avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Alcumus Info Exchange équivalent à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Alcumus Info Exchange associé doit être établie.  
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Alcumus Info Exchange.

Pour configurer et tester l’authentification unique Azure AD avec Alcumus Info Exchange, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Alcumus informations Exchange](#creating-a-alcumus-info-exchange-test-user)** pour avoir un équivalent de Britta Simon dans Alcumus Info Exchange lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Vous allez maintenant activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Alcumus Info Exchange.

**Pour configurer l’authentification unique Azure AD avec Alcumus Info Exchange, procédez comme suit :**

1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Alcumus Info Exchange**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Alcumus Info Exchange**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][7]
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit : 
   
    ![Authentification unique Azure AD][8]
   
    a. dans la zone de texte **URL de réponse** , saisissez l’URL du consommateur installé pour vous par l’équipe du support technique Exchange Info Alcumus.
   
   > [!NOTE]
   > Si vous ne connaissez pas la valeur adéquate, contactez l’équipe du support technique Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).
   > 
   > 
   
    b. Cliquez sur **Next**.
4. Dans la page **Configurer l’authentification unique à Alcumus Info Exchange**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.
   
    ![Qu’est-ce qu’Azure AD Connect ?][9]
5. Contactez l’équipe de support Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), fournissez-leur un fichier de métadonnées et faites-leur savoir qu’ils doivent activer SSO pour vous.
6. Dans le Portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
    ![Qu’est-ce qu’Azure AD Connect ?][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Qu’est-ce qu’Azure AD Connect ?][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur Suivant.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.


1. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png) 
2. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-alcumus-info-exchange-test-user"></a>Création d’un utilisateur de test Alcumus informations Exchange
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Alcumus Info Exchange.

**Pour créer un utilisateur appelé Britta Simon dans Alcumus Info Exchange, procédez comme suit :**

1. Contactez l’équipe de support Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Alcumus Info Exchange.

![Affecter des utilisateurs][200]

**Pour attribuer Britta Simon à Alcumus Info Exchange, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Alcumus Info Exchange**.
   
    ![Affecter des utilisateurs][202]
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Lorsque vous cliquez sur la vignette Alcumus Info Exchange dans le volet d’accès, vous devez vous connecter automatiquement à votre application Alcumus Info Exchange.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png



<!--HONumber=Feb17_HO2-->


