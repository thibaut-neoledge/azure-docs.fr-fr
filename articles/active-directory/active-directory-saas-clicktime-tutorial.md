---
title: "Didacticiel : Intégration d’Azure Active Directory à ClickTime | Microsoft Docs"
description: "Apprenez à utiliser ClickTime avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 786ffe97c794aa44dc5d2b8e1a9597387752409c


---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Didacticiel : Intégration d’Azure Active Directory à ClickTime
Dans ce didacticiel, vous allez apprendre à intégrer ClickTime à Azure Active Directory (Azure AD).

L’intégration de ClickTime à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Clicktime
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ClickTime (par le biais de l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables
Pour configurer l’intégration d’Azure AD avec ClickTime, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement ClickTime pour lequel l’authentification unique est activée

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

1. Ajout de ClickTime à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-clicktime-from-the-gallery"></a>Ajout de ClickTime à partir de la galerie
Cette section décrit l’activation de l’intégration d’applications pour ClickTime.

### <a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour ClickTime, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-clicktime-tutorial/tic700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-clicktime-tutorial/tic749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-clicktime-tutorial/tic749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **ClickTime**.
   
   ![Galerie d’applications](./media/active-directory-saas-clicktime-tutorial/tic777275.png "Application gallery")
7. Dans le volet des résultats, sélectionnez **ClickTime**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ClickTime, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ClickTime équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur ClickTime associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans ClickTime.

Pour configurer et tester l’authentification unique Azure AD avec ClickTime, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test ClickTime](#creating-a-clicktime-test-user)** pour avoir un équivalent de Britta Simon dans ClickTime, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ClickTime avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  

> [!IMPORTANT]
> Pour être en mesure de configurer l’authentification unique sur votre client ClickTime, vous devez au préalable contacter le support technique ClickTime pour faire activer cette fonctionnalité.
> 
> 

**Pour configurer l’authentification unique Azure AD avec ClickTime, procédez comme suit :**

1. Dans le portail Azure Classic, puis dans la page d’intégration d’applications **ClickTime**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Activer l'authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777277.png "Enable single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ClickTime**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configure single sign-on")
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 
   
    a. Dans la zone de texte **Identificateur**, tapez l’URL au format suivant : **https://app.clicktime.com/sp/**
   
    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : **https://app.clicktime.com/Login/**
   
    c. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur ClickTime**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configure single sign-on")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ClickTime en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Preferences**, puis sur **Security Settings**.
7. Dans la section de configuration **Single Sign-On Preferences** , procédez comme suit :
   
   ![Security Settings](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Security Settings")
   
   a.  Sélectionnez **Autoriser** la connexion à l’aide de l’authentification unique (SSO) avec **Azure AD**.
   
   b.  Dans la page **Configurer l’authentification unique sur ClickTime** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Identity Provider Endpoint**.
   
   c.  Ouvrez le certificat codé en base 64 dans le **Bloc-notes**, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate**.
   
   d.  Cliquez sur **Save**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à ClickTime, les utilisateurs d’Azure AD doivent être approvisionnés dans ClickTime.  
Dans le cas de ClickTime, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre client **ClickTime** .
2. Dans la barre d’outils située en haut, cliquez sur **Company**, puis sur **People**.
   
   ![Personnes](./media/active-directory-saas-clicktime-tutorial/tic777282.png "People")
3. Cliquez sur **Add Person**.
   
   ![Add Person](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Add Person")
4. Dans la section New Person, procédez comme suit :
   
   ![People](./media/active-directory-saas-clicktime-tutorial/tic777284.png "People")
   
   a.  Dans la zone de texte **email address** , tapez l’adresse de messagerie de votre compte Azure AD.
   
   b.  Dans la zone de texte **full name** , tapez le nom de votre compte Azure AD.  
   
   > [!NOTE]
   > Si vous le souhaitez, vous pouvez définir d’autres propriétés relatives à l’objet de la nouvelle personne.
   > 
   > 
   
   c.  Cliquez sur **Save**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par ClickTime pour approvisionner des comptes d’utilisateurs Azure AD.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ClickTime.

![Affecter des utilisateurs][200]

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter Britta Simon à ClickTime, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **ClickTime**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

## <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ClickTime dans le volet d’accès, vous devez être connecté automatiquement à votre application ClickTime.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


