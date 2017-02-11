---
title: "Didacticiel : Intégration d’Azure Active Directory à HPE SaaS | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et HPE SaaS."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 314003d6-ca66-4456-88c3-934254d4a9a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 92d61ff08b70e89b5e5310cf4b0f7fb88abf3ef9


---
# <a name="tutorial-azure-active-directory-integration-with-hpe-saas"></a>Didacticiel : Intégration d’Azure Active Directory à HPE SaaS
L’objectif de ce didacticiel est de vous montrer comment intégrer HPE SaaS dans Azure AD (Azure Active Directory).  
L’intégration de HPE SaaS dans Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à HPE SaaS.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à HPE SaaS (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à HPE SaaS, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement HPE SaaS pour lequel l’authentification unique est activée

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

1. Ajout de HPE SaaS à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-hpe-saas-from-the-gallery"></a>Ajout de HPE SaaS à partir de la galerie
Pour configurer l’intégration de HPE SaaS à Azure AD, vous devez ajouter HPE SaaS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HPE SaaS à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **HPE SaaS**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_01.png)
7. Dans le volet de résultats, sélectionnez **HPE SaaS**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec HPE SaaS, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HPE SaaS équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur HPE SaaS associé doit être établie.  
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans HPE SaaS.

Pour configurer et tester l’authentification unique Azure AD avec HPE SaaS, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test HPE SaaS](#creating-a-hpesaas-test-user)** : pour avoir un équivalent de Britta Simon dans HPE SaaS lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application HPE SaaS.

**Pour configurer l’authentification unique Azure AD avec HPE SaaS, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **HPE SaaS**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à HPE SaaS**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_03.png) 
3. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_04.png) 

    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par les utilisateurs pour se connecter à votre application HPE SaaS : **“https://login.saas.hpe.com/msg”**. Les clients peuvent également remplacer cette URL par une URL spécifique à l’application.

    b. Cliquez sur **Next**.


1. Dans la page **Configurer l’authentification unique sur HPE SaaS** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_05.png) 
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Next**.
2. Pour configurer l’authentification unique pour votre application, contactez l’équipe du support technique HPE SaaS et joignez le fichier de métadonnées téléchargé à votre courrier électronique. Vous pouvez ainsi les configurer pour l’intégration de l’authentification unique.
3. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_09.png) 

1. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
2. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_03.png) 
3. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_04.png) 
4. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
5. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_06.png) 
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_07.png) 
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-hpe-saas-test-user"></a>Création d’un utilisateur de test HPE SaaS
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans HPE SaaS. Collaborez avec l’équipe du support technique HPE SaaS pour ajouter les utilisateurs au compte HPE SaaS. 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique HPE SaaS.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à HPE SaaS.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à HPE SaaS, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **HPE SaaS**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Quand vous cliquez sur la vignette HPE SaaS dans le volet d’accès, vous devez être connecté automatiquement à votre application HPE SaaS.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


