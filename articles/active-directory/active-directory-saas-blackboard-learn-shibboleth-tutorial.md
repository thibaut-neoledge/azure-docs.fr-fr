---
title: "Didacticiel : Intégration d’Azure Active Directory à Blackboard Learn - Shibboleth | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Blackboard Learn - Shibboleth."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e435cbb4-c0f0-400e-943c-5c923fa8ddf2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 0fd15c1d01959194ff25ac7f93c015a8edd38057


---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Didacticiel : Intégration d’Azure Active Directory à Blackboard Learn - Shibboleth
Dans ce didacticiel, vous allez apprendre à intégrer Blackboard Learn - Shibboleth à Azure Active Directory (Azure AD).

L’intégration de Blackboard Learn- Shibboleth dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Blackboard Learn - Shibboleth
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Blackboard Learn - Shibboleth (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Blackboard Learn - Shibboleth, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement actif à connexion unique à Blackboard Learn - Shibboleth

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

1. Ajout de Blackboard Learn - Shibboleth à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Ajout de Blackboard Learn - Shibboleth à partir de la galerie
Pour configurer l’intégration de Blackboard Learn - Shibboleth à Azure AD, vous devez ajouter Blackboard Learn - Shibboleth de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Blackboard Learn - Shibboleth à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]

6. Dans la zone de recherche, tapez **Blackboard Learn - Shibboleth**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_01.png)

7. Dans le volet de résultats, sélectionnez **Blackboard Learn - Shibboleth**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Blackboard Learn - Shibboleth avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Blackboard Learn - Shibboleth équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Blackboard Learn - Shibboleth associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Blackboard Learn - Shibboleth.

Pour configurer et tester l’authentification unique Azure AD avec Blackboard Learn - Shibboleth, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Blackboard Learn - Shibboleth](#creating-a-blackboard-learn-shibboleth-test-user)** pour avoir un équivalent de Britta Simon dans Blackboard Learn - Shibboleth lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Blackboard Learn - Shibboleth.

**Pour configurer l’authentification unique Azure AD avec Blackboard Learn - Shibboleth, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration d’application **Blackboard Learn - Shibboleth**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Blackboard Learn - Shibboleth ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_03.png) 

3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_04.png) 
   
    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Blackboard Learn - Shibboleth au format suivant : **https://\<votreserveurblackboardlearn\>.blackboardlearn.com/Shibboleth.sso/Login**
   
    b. Dans la zone de texte **Identificateur**, entrez l’URL au format suivant : **https://\<votreserveurblackboardlearn\>.blackboardlearn.com/shibboleth-sp**
   
    c. Dans la zone de texte **URL de réponse**, entrez l’URL au format suivant : **https://\<votreserveurblackboardlearn\>.blackboardlearn.com/Shibboleth.sso/SAML2/POST**
   
    > [!NOTE]
    > Toutes ces valeurs se trouvent dans le document Métadonnées de fédération fourni par votre partenaire Blackboard Learn.
    > 
    > 
   
    d. click **Suivant**

4. Dans la page **Configurer l’authentification unique sur Blackboard Learn - Shibboleth** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_05.png)
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.

5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez votre partenaire Blackboard Learn - Shibboleth et fournissez-lui les éléments suivants :
   
    • Les **métadonnées**
   
    • **L’URL de l’émetteur**
   
    • **L’URL d’authentification unique SAML**
   
    • **L’URL du service de déconnexion unique**

6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.

6. Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_06.png) 
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-an-blackboard-learn---shibboleth-test-user"></a>Création d’un utilisateur de test Blackboard Learn - Shibboleth
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Blackboard Learn - Shibboleth. Travaillez avec votre partenaire Blackboard Learn pour ajouter des utilisateurs dans la formation d’unicolore - plateforme de Shibboleth.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Blackboard Learn - Shibboleth.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Blackboard Learn - Shibboleth, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Blackboard Learn - Shibboleth**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Blackboard Learn - Shibboleth dans le volet d’accès, vous devez être connecté automatiquement à votre application Blackboard Learn - Shibboleth.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


