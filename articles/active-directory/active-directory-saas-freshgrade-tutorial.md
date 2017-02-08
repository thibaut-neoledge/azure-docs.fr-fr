---
title: "Didacticiel : Intégration d’Azure Active Directory à FreshGrade | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et FreshGrade."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1055bba6-f4df-462e-bc9b-1ad5ada0f638
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 04c401cbb0b073a791d074f795085e3bd0b8cd8e


---
# <a name="tutorial-azure-active-directory-integration-with-freshgrade"></a>Didacticiel : Intégration d’Azure Active Directory à FreshGrade
Dans ce didacticiel, vous allez apprendre à intégrer FreshGrade à Azure Active Directory (Azure AD).

L’intégration de FreshGrade dans Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD les utilisateurs qui ont accès à FreshGrade. Vous pouvez leur permettre de se connecter automatiquement à FreshGrade (authentification unique) avec leurs comptes Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD dans FreshGrade, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement FreshGrade pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FreshGrade depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-freshgrade-from-the-gallery"></a>Ajout de FreshGrade depuis la galerie
Pour configurer l’intégration de FreshGrade à Azure AD, vous devez ajouter FreshGrade depuis la galerie à votre liste d’applications SaaS gérées.

**Procédez comme suit pour ajouter FreshGrade à partir de la galerie :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **FreshGrade**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_01.png)
7. Dans le volet des résultats, sélectionnez **FreshGrade**, puis cliquez sur **Terminer** pour ajouter l’application.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et testez l’authentification unique Azure AD avec FreshGrade avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur test FreshGrade équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur FreshGrade associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans FreshGrade.

Pour configurer et tester l’authentification unique Azure AD avec FreshGrade, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test FreshGrade](#creating-a-freshgrade-test-user)** pour avoir un équivalent de Britta Simon dans FreshGrade lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure Classic et configurez l’authentification unique dans votre application FreshGrade.

**Procédez comme suit pour configurer l’authentification unique Azure AD avec FreshGrade :**

1. Dans le portail Classic, sur la page d’intégration de l’application **FreshGrade**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à FreshGrade ?**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_04.png) 
   
    a. Dans la zone de texte **URL DE CONNEXION**, entrez une URL au format suivant : `https://<your-subdomain>.freshgrade.com` ou `https://<your-subdomain>.onboarding.freshgrade.com`.
   
    b. Cliquez sur **Suivant**
4. Procédez comme suit dans la page **Configurer l’authentification unique sur FreshGrade** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_05.png)
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez votre équipe de support de à l’adresse [Support@freshgrade.com](mailTo:support@freshgrade.com) et envoyez-lui les éléments suivants :
   
    • Les **métadonnées téléchargées**
   
    • **L’URL SSO SAML**
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-a-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-freshgrade-test-user"></a>Création d’un utilisateur de test FreshGrade
Dans cette section, vous créez un utilisateur appelé Britta Simon dans FreshGrade. Collaborez avec l’équipe du support technique FreshGrade pour ajouter des utilisateurs dans la plateforme FreshGrade.
En cas de problèmes liés à la création d’utilisateurs, contactez le support à l’adresse [support@freshgrade.com](mailTo:support@freshgrade.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FreshGrade.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à FreshGrade, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **FreshGrade**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils
6. située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette FreshGrade dans le volet d’accès, vous devez être connecté automatiquement à votre application FreshGrade.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


