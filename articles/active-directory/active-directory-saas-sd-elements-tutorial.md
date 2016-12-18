---
title: "Didacticiel : Intégration d’Azure Active Directory à SD Elements | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SD Elements."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53aa0a84a7f22c8cda5144eb6e1b82f38b72acb8


---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Didacticiel : Intégration d’Azure Active Directory à SD Elements
L’objectif de ce didacticiel est de vous montrer comment intégrer SD Elements dans Azure Active Directory (Azure AD).  
L’intégration de SD Elements à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à SD Elements.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SD Elements (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : Azure Active Directory. 

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec SD Elements, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement SD Elements pour lequel l’authentification unique est activée

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

1. Ajout de SD Elements à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sd-elements-from-the-gallery"></a>Ajout de SD Elements à partir de la galerie
Pour configurer l’intégration de SD Elements avec Azure AD, vous devez ajouter SD Elements disponible dans la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SD Elements à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]

6. Dans la zone de recherche, tapez **SD Elements**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)

7. Dans le volet de résultats, sélectionnez **SD Elements**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec SD Elements avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SD Elements équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur SD Elements associé doit être établie.  
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans SD Elements.

Pour configurer et tester l’authentification unique Azure AD avec SD Elements, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d'un utilisateur de test SD Elements](#creating-a-sd-elements-test-user)** : pour avoir un équivalent de Britta Simon dans SD Elements lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L'objectif de cette section est d'activer l'authentification unique Azure AD dans le portail Azure Classic et de configurer l'authentification unique dans votre application SD Elements.

Votre application SD Elements attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d'attributs personnalisés à la configuration des **attributs du jeton SAML** . La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) 

**Pour configurer l’authentification unique Azure AD avec SD Elements, procédez comme suit :**

1. Dans le portail Azure Classic, puis sur la page d’intégration d’application **SD Elements**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à SD Elements ?**, sélectionnez **Authentification unique Azure AD**, puis sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) 

3. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) 

    a. Dans la zone de texte **Émetteur**, entrez l'URL de l'émetteur de votre client au format modèle suivant : *https://\<nom de votre locataire\>.sdelements.com/sso/saml2/metadata*

    b. Dans la zone de texte **Émetteur**, entrez l'URL de réponse de votre client au format suivant : *https://\<nom de votre locataire\>.sdelements.com/sso/saml2/acs/*       

    > [!NOTE] 
    > Si vous avez besoin des véritables URL de l'émetteur et de réponse pour votre client, contactez votre [équipe de support technique SD Elements](mailto:support@sdelements.com).

    c. Cliquez sur **Suivant**.


1. Sur la page **Configurer l’authentification unique sur SD Elements** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) 
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
2. Pour activer l'authentification unique, contactez votre [équipe de support technique SD Elements](mailto:support@sdelements.com) et fournissez-leur le fichier de certificat téléchargé.
3. Dans une autre fenêtre de navigateur, connectez-vous à votre application SD Elements en tant qu’administrateur.
4. Dans le menu du haut, cliquez sur Système, puis sur Authentification unique. 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 
5. Dans la boîte de dialogue **Paramètres d’authentification unique** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Pour **SSO Type**, sélectionnez **SAML**.
   
    b. Dans la page **Configurer l’authentification unique sur SD Elements** du portail Azure Classic, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **Identity Provider Entity ID**.
   
    c. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur SD Elements**, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Identity Provider Single Sign-On Service**.
   
    d. Cliquez sur **Enregistrer**.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]
8. Dans le menu situé en haut, cliquez sur **Attributs** to open the **SAML Token Attributs** . 
   
    ![Configurer l’authentification unique][21]
9. Pour chaque ligne dans le tableau ci-dessus, procédez comme suit :
   
   | Nom de l'attribut | Valeur de l’attribut |
   | --- | --- |
   | email |user.mail |
   | firstname |user.givenname |
   | lastname |user.surname |

    a. Cliquez sur **Ajouter un attribut utilisateur**. 

    ![Configurer l’authentification unique][23]

    b. Dans la zone de texte **Nom de l’attribut**, entrez le **nom de l’attribut**, puis, en tant que **Valeur de l’attribut**, sélectionnez la valeur d’attribut pour cette ligne.

    ![Configurer l’authentification unique][22]

    c. Cliquez sur **Ajouter un attribut utilisateur**. 

    ![Configurer l’authentification unique][23]

1. Cliquez sur **Appliquer les modifications**. 
   
    ![Configurer l’authentification unique][24]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-sd-elements-test-user"></a>Création d'un utilisateur de test SD Elements
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SD Elements. Dans le cas de SD Elements, la création d'utilisateurs SD Elements est une tâche manuelle.

**Pour créer un utilisateur appelé Britta Simon dans SD Elements, procédez comme suit :**

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise SD Elements en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur Gestion des utilisateurs, puis sur Utilisateurs.
   
   ![Création d'un utilisateur de test SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 
3. Cliquez sur Ajouter un nouvel utilisateur.
   
   ![Création d'un utilisateur de test SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) 
4. Dans la boîte de dialogue Ajouter un nouvel utilisateur, procédez comme suit :
   
   ![Création d'un utilisateur de test SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
   a. Dans la zone de texte **E-mail** , entrez l’adresse de messagerie de Simon Britta dans Azure AD.
   
   b. Dans la zone de texte **First Name**, tapez **Britta**.
   
   c. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   d. Pour **Role**, sélectionnez **User**. 
   
   e. Cliquez sur **Créer l’utilisateur**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SD Elements.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SD Elements, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **SD Elements**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste **Utilisateurs**, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Lorsque vous cliquez sur la mosaïque SD Elements dans le volet d’accès, vous devez être connecté automatiquement à votre application SD Elements.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


