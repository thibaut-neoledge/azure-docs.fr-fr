---
title: "Didacticiel : Intégration d’Azure Active Directory à RunMyProcess | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et RunMyProcess."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 223a0abfcd1ae2997e4d2facfd065cf8dc721668


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Didacticiel : Intégration d’Azure Active Directory à RunMyProcess
L’objectif de ce didacticiel est de vous montrer comment intégrer RunMyProcess à Azure Active Directory (Azure AD).

L’intégration de RunMyProcess à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à RunMyProcess.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à RunMyProcess (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables
Pour configurer l’intégration d’Azure AD à RunMyProcess, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement RunMyProcess pour lequel l’authentification unique est activée

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

1. Ajout de RunMyProcess à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-runmyprocess-from-the-gallery"></a>Ajout de RunMyProcess à partir de la galerie
Pour configurer l’intégration de RunMyProcess à Azure AD, vous devez ajouter RunMyProcess à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter RunMyProcess à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **RunMyProcess**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. Dans le volet de résultats, sélectionnez **RunMyProcess**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec RunMyProcess avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur RunMyProcess équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur RunMyProcess associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans RunMyProcess.

Pour configurer et tester l’authentification unique Azure AD avec RunMyProcess, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test RunMyProcess](#creating-a-runmyprocess-test-user)** pour avoir un équivalent de Britta Simon dans RunMyProcess lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application RunMyProcess.

**Pour configurer l’authentification unique Azure AD avec RunMyProcess, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration d’application **RunMyProcess**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à RunMyProcess**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
   
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://live.runmyprocess.com/live/<tenant id>`.
   
    b. Cliquez sur **Suivant**
   
   > [!NOTE]
   > Notez que vous devez mettre à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’équipe de support technique de RunMyProcess via <mailto:support@runmyprocess.com>.
   > 
   > 
4. Dans la page **Configurer l’authentification unique sur RunMyProcess**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre client RunMyProcess en tant qu’administrateur.
6. Dans le volet de navigation à gauche, cliquez sur **Compte** et sélectionnez **Configuration**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. Accédez à la section **Méthode d’authentification** et suivez la procédure ci-dessous :
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
   
    a. Pour **Method**, sélectionnez **SSO with Samlv2**.
   
    b. Dans la zone de texte **SSO redirect** (Redirection d’authentification unique), copiez la valeur de **l’URL d’authentification unique SAML** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    c. Dans la zone de texte **Logout redirect** (Redirection de déconnexion), copiez la valeur de **l’URL du service de déconnexion unique** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    d. Dans la zone de texte **Name Id Format** (Format d’ID de nom), copiez la valeur du **format de l’identificateur de nom** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    e. Copiez le contenu du fichier de certificat téléchargé, puis collez-le dans la zone de texte **Certificat**. 
   
    f. Cliquez sur l’icône **Enregistrer**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
9. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-runmyprocess-test-user"></a>Création d’un utilisateur de test de RunMyProcess
Pour permettre aux utilisateurs Azure AD de se connecter à RunMyProcess, vous devez les approvisionner dans RunMyProcess. Dans le cas de RunMyProcess, l’approvisionnement est une tâche manuelle.

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous au site d’entreprise RunMyProcess en tant qu’administrateur.
2. Dans le volet de navigation à gauche, cliquez sur **Compte** et sélectionnez **Utilisateurs**, puis cliquez sur **Nouvel utilisateur**.
   
   ![New User](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "New User")
3. Dans la section **User Settings** , procédez comme suit :
   
   ![Profil](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profile")
   
   a. Tapez le nom et l’adresse électronique d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Name** et **E-mail**.
   
   b. Sélectionnez des options pour **IDE language**, **Language** et **Profile**.
   
   c. Sélectionnez **Send account creation e-mail to me**.
   
   d. Cliquez sur **Enregistrer**.
   
   > [!NOTE]
   > Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par RunMyProcess, pour approvisionner des comptes utilisateur Azure Active Directory.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à RunMyProcess.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à RunMyProcess, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **RunMyProcess**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque RunMyProcess dans le volet d’accès, vous devez être connecté automatiquement à votre application RunMyProcess.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


