---
title: "Didacticiel : Intégration d’Azure Active Directory à Pacific Timesheet | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pacific Timesheet."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e546e8ba-821a-4942-9545-c84b0670beab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 603505ed07f7ab38470b669295acf08a35d9f38e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pacific-timesheet"></a>Didacticiel : Intégration d’Azure Active Directory avec Pacific Timesheet
Dans ce didacticiel, vous allez apprendre à intégrer Pacific Timesheet à Azure Active Directory (Azure AD).

L’intégration de Pacific Timesheet à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Pacific Timesheet.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Pacific Timesheet (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration de Pacific Timesheet à Azure AD, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Pacific Timesheet pour lequel l’authentification unique (SSO) est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
>  

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de Pacific Timesheet à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-pacific-timesheet-from-the-gallery"></a>Ajouter Pacific Timesheet à partir de la galerie
Pour configurer l’intégration de Pacific Timesheet à Azure AD, vous devez ajouter Pacific Timesheet à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Pacific Timesheet à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Pacific Timesheet**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_01.png)
7. Dans le volet de résultats, sélectionnez **Pacific Timesheet**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Pacific Timesheet, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique (SSO) fonctionne, Azure AD doit savoir qui est l’utilisateur Pacific Timesheet équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Pacific Timesheet associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Pacific Timesheet. Pour configurer et tester l’authentification unique Azure AD avec Pacific Timesheet, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Pacific Timesheet](#creating-a-pacific-timesheet-test-user)** pour avoir un équivalent de Britta Simon dans Pacific Timesheet lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique (SSO) Azure AD dans le portail Azure Classic et de configurer l’authentification unique (SSO) dans votre application Pacific Timesheet.

**Pour configurer l’authentification unique Azure AD avec Pacific Timesheet, procédez comme suit :**

1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique][6]
2. Dans le portail Azure Classic, dans la page d’intégration d’application **Pacific Timesheet**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][7] 
3. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Pacific Timesheet**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_06.png)
4. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, configurez l’application en **mode lancé par le fournisseur d’identité** en procédant comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_07.png)
  1. Dans la zone de texte Identificateur, tapez une URL au format suivant : `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`
  2. Dans la zone de texte URL de réponse, tapez une URL au format suivant : `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`.
  3. Cliquez sur **Suivant**.

5. Dans la page **Configurer l’authentification unique sur Pacific Timesheet**. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_09.png)
6. Pour que l’authentification unique soit configurée pour votre application, contactez l’équipe du support technique Pacific Timesheet. Notez que vous devez envoyer un e-mail avec l’URL de l’émetteur, les valeurs d’URL d’authentification unique SAL de la page **Configurer l’authentification unique sur Pacific Timesheet**, et attacher un certificat téléchargé.

7. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_05.png) 
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_06.png) 
  1. Dans la zone de texte **First Name**, tapez **Britta**.   
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_08.png) 
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

### <a name="create-a-pacific-timesheet-test-user"></a>Créer un utilisateur de test Pacific Timesheet
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Pacific Timesheet. Contactez l’équipe de support technique Pacific Timesheet pour créer un utilisateur dans l’application.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pacific Timesheet.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Pacific Timesheet, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Pacific Timesheet**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_10.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Pacific Timesheet dans le volet d’accès, vous êtes connecté automatiquement à votre application Pacific Timesheet.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_205.png

