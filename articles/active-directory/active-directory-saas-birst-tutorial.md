---
title: "Didacticiel : Intégration d’Azure Active Directory à Birst Agile Business Analytics | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Birst Agile Business Analytics."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0f68cab69ea97dc6eaf08d03bc30783de9e7259e


---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Didacticiel : Intégration d’Azure Active Directory à Birst Agile Business Analytics
L’objectif de ce didacticiel est de vous montrer comment intégrer Birst Agile Business Analytics dans Azure AD (Azure Active Directory).

L’intégration de Birst Agile Business Analytics dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Birst Agile Business Analytics.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Birst Agile Business Analytics (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Birst Agile Business Analytics, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Birst Agile Business Analytics pour lequel l’authentification unique est activée

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

1. Ajout de Birst Agile Business Analytics à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Ajout de Birst Agile Business Analytics à partir de la galerie
Pour configurer l’intégration de Birst Agile Business Analytics à Azure AD, vous devez ajouter Birst Agile Business Analytics à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Birst Agile Business Analytics à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Birst Agile Business Analytics**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/tutorial_birst_01.png)
7. Dans le volet de résultats, sélectionnez **Birst Agile Business Analytics**, puis cliquez sur **Terminé** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/tutorial_birst_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Birst Agile Business Analytics, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Birst Agile Business Analytics équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Birst Agile Business Analytics associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** (Nom d’utilisateur) dans Birst Agile Business Analytics.

Pour configurer et tester l’authentification unique Azure AD avec Birst Agile Business Analytics, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Birst Agile Business Analytics](#creating-a-birst-agile-business-analytics-test-user)** pour avoir un équivalent de Britta Simon dans Birst Agile Business Analytics, lié à sa représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Birst Agile Business Analytics.

**Pour configurer l’authentification unique Azure AD avec Birst Agile Business Analytics, procédez comme suit :**

1. Sur la page d’intégration d’application **Birst Agile Business Analytics** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Birst Agile Business Analytics**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-birst-tutorial/tutorial_birst_03.png) 
3. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-birst-tutorial/tutorial_birst_04.png) 

    a. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Birst Agile Business Analytics. Utilisez le format suivant : **https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID**.
    L’URL dépend du centre de données dans lequel se trouve votre compte Birst. Avec le centre de données des États-Unis, utilisez **https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID** et avec le centre de données européen, utilisez **https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID**

    b. Cliquez sur **Suivant**.


1. Sur la page **Configure single sign-on at Birst Agile Business Analytics** (Configurer l’authentification unique sur Birst Agile Business Analytics), procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-birst-tutorial/tutorial_birst_05.png) 
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
2. Pour configurer l’authentification unique pour votre application, contactez votre équipe du support technique Birst Agile Business Analytics via [info@birst.com](emailTo:info@birst.com) , puis joignez le fichier de certificat téléchargé à votre message. Indiquez également l’URL d’authentification unique SAML, l’URL de déconnexion et l’URL de l’émetteur dans le cadre de l’intégration de l’authentification unique.

> [!NOTE]
> Indiquez à l’équipe Birst que cette intégration nécessite l’algorithme SHA256 (SHA1 n’est pas pris en charge) pour que l’authentification unique puisse être définie sur le serveur approprié, comme **app2101** , etc.
> 
> 

1. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
2. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Création d’un utilisateur de test Birst Agile Business Analytics
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Birst Agile Business Analytics. Collaborez avec l’équipe du support technique Birst Agile Business Analytics pour ajouter des utilisateurs au compte Birst. 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Birst Agile Business Analytics.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Birst Agile Business Analytics.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Birst Agile Business Analytics, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Birst Agile Business Analytics**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-birst-tutorial/tutorial_birst_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Birst Agile Business Analytics dans le volet d’accès, vous devez être connecté automatiquement à votre application Birst Agile Business Analytics.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-birst-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-birst-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-birst-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-birst-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-birst-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-birst-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-birst-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-birst-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-birst-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-birst-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-birst-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-birst-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-birst-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


