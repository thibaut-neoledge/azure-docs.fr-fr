---
title: "Didacticiel : Intégration d’Azure Active Directory à AppBlade | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et AppBlade."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 82e5a947d48f8a289deb2f6e85bbb47990a9fcd7
ms.openlocfilehash: a378ca7be4c5a7df066c5450f9205c02c2acda0f


---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Didacticiel : Intégration d’Azure Active Directory à AppBlade
L’objectif de ce didacticiel est de vous montrer comment intégrer AppBlade dans Azure Active Directory (Azure AD).  
L’intégration de AppBlade dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à AppBlade.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à AppBlade (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à AppBlade, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement AppBlade pour lequel l’authentification unique est activée

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

1. Ajout de AppBlade à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-appblade-from-the-gallery"></a>Ajout de AppBlade à partir de la galerie
Pour configurer l’intégration de AppBlade à Azure AD, vous devez ajouter AppBlade à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter AppBlade à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **AppBlade**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_01.png)

1. Dans le volet de résultats, sélectionnez **AppBlade**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec AppBlade au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur AppBlade équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur AppBlade associé doit être établie.  
Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans AppBlade.

Pour configurer et tester l’authentification unique Azure AD avec AppBlade, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test AppBlade](#creating-a-appblade-test-user)** pour avoir un équivalent de Britta Simon dans AppBlade lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application AppBlade.

**Pour configurer l’authentification unique Azure AD avec AppBlade, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **AppBlade**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à AppBlade**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_03.png) >
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_04.png) 

    a. Dans la zone de texte URL d’authentification, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application AppBlade, au format suivant : **« https://nom_entreprise.appblade.com/saml/id_client »**.

    b. Cliquez sur **Suivant**.


1. Dans la page **Configurer l’authentification unique sur AppBlade** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_05.png) 
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
2. Pour configurer l’authentification unique pour votre application, contactez votre équipe de support technique AppBlade via **support@appblade.com** et joignez le fichier de métadonnées téléchargé à votre courrier électronique. Demandez-lui également de configurer l’**URL de l’émetteur d’authentification unique** sur **https://appblade.com/saml**. Ce paramètre est nécessaire pour que l'authentification unique fonctionne.
3. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_06.png) 
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
    
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-appblade-test-user"></a>Création d’un utilisateur de test AppBlade
L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans AppBlade. AppBlade prend en charge l'approvisionnement juste-à-temps, option activée par défaut. **Vérifiez que votre nom de domaine a été correctement configuré avec AppBlade pour l’approvisionnement des utilisateurs. Après cela seulement, l’approvisionnement juste-à-temps des utilisateurs fonctionne.**

Si l’utilisateur possède une adresse de messagerie se terminant par le domaine configuré par AppBlade pour votre compte, il devient automatiquement membre du compte avec le niveau d’autorisation que vous spécifiez, « De base » (utilisateur de base pouvant uniquement installer les applications), « Membre de l’équipe » (utilisateur pouvant télécharger les nouvelles versions d’application et gérer les projets) ou « Administrateur » (utilisateur disposant des privilèges d’administrateur complets sur le compte). En principe, l’utilisateur De base serait sélectionné par défaut, puis promu manuellement par le biais d’une connexion Administrateur (AppBlade doit configurer une connexion administrateur par courrier électronique à l'avance ou promouvoir un utilisateur pour le compte du client après la connexion).

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à AppBlade s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe de support AppBlade.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à AppBlade.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à AppBlade, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **AppBlade**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Quand vous cliquez sur la vignette AppBlade dans le volet d’accès, vous devez être connecté automatiquement à votre application AppBlade.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


