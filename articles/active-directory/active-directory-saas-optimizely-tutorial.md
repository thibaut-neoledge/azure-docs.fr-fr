---
title: "Didacticiel : Intégration d’Azure Active Directory à Optimizely |Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Optimizely."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 2275daf25fd916932dbf54a7b069ecbfcf3e68cf
ms.lasthandoff: 12/08/2016


---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Didacticiel : Intégration d’Azure Active Directory avec Optimizely
Dans ce didacticiel, vous allez apprendre à intégrer Optimizely avec Azure Active Directory (Azure AD).

L’intégration d’Optimizely à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Optimizely.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Optimizely (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Optimizely, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement **Optimizely** pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Optimizely à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-optimizely-from-the-gallery"></a>Ajout d’Optimizely à partir de la galerie
Pour configurer l’intégration d’Optimizely avec Azure AD, vous devez ajouter Optimizely à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Optimizely à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]

6. Dans la zone de recherche, tapez **Optimizely**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. Dans le volet des résultats, sélectionnez **Optimizely**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Optimizely avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Optimizely équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Optimizely associé doit être établie.
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Optimizely.

Pour configurer et tester l’authentification unique Azure AD avec Optimizely, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Optimizely](#creating-an-optimizely-test-user)** pour avoir un équivalent de Britta Simon dans Optimizely lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Optimizely.

L’application Optimizely s’attend à ce que les assertions SAML contiennent un attribut nommé « email ». La valeur de l’attribut « email » doit être un message électronique reconnu par Optimizely et qui peut être authentifié par Azure AD. Configurez la revendication « email » pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attributs** de l’application. La capture d’écran suivante montre un exemple : 

![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 

**Pour configurer l’authentification unique Azure AD avec Optimizely, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Optimizely**, dans le menu situé en haut, cliquez sur **Attributs**.
   
    ![Configurer l’authentification unique][5]

2. Dans la boîte de dialogue des attributs du jeton SAML, ajoutez l’attribut « email ».
   
    a. Cliquez sur **add user attribute** to open the **Ajouter un attribut utilisateur** . 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)
   
    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom de l’attribut « email ».
   
    c. À partir de la liste **Valeur de l’attribut** , sélectionnez la valeur d’attribut « userprincipalname » ou toute valeur qui contient un e-mail reconnu par Azure AD et Optimizely.
   
    d. Cliquez sur **Terminé**.

3. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique][6]

4. Dans le portail Classic, dans la page d’intégration d’application **Optimizely**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][7] 

5. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Optimizely**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Sur la page **Configurer les paramètres d’application** , procédez comme suit : 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    a. Dans la zone de texte **URL d’authentification**, tapez : `https://app.optimizely.net/contoso`

    b. Dans la zone de texte **Identificateur**, tapez : `urn:auth0:optimizely:contoso`

    c. Cliquez sur **Next**. 

    > [!NOTE] 
    > Les valeurs pour **URL de connexion** et **Identificateur** sont uniquement des espaces réservés pour les valeurs réelles. Vous trouverez des instructions sur l’acquisition des valeurs réelles à partir d’Optimizely plus loin dans ce didacticiel.

1. Sur la page **Configurer l’authentification unique sur Optimizely** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Copiez l’ **URL du service d’authentification unique**.

2. Pour obtenir la configuration de l’authentification unique pour votre application, contactez votre responsable de compte Optimizely et envoyez-lui les informations suivantes :
   
   * Votre certificat téléchargé. 
   * L’URL du service d’authentification unique.
     
     En réponse à votre e-mail, Optimizely vous fournit l’URL de connexion (authentification unique initiée par le fournisseur de service) et l’identificateur (ID d’entité du fournisseur de service).

3. Revenez à la page de boîte de dialogue **Configurer les paramètres d’application** , puis procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)
   
    a. Dans la zone de texte **URL de connexion**, entrez **l’URL d’authentification unique initiée par le fournisseur de service** fournie par Optimizely.
   
    b. Dans la zone de texte **Identificateur**, entrez **l’ID d’entité du fournisseur de service** fourni par Optimizely.
   
    c. Cliquez sur **Suivant**.

4. Sur la page **Configurer l’authentification unique sur Optimizely** , procédez comme suit :
   
    ![Authentification unique Azure AD][10]
   
    a. Sélectionnez la confirmation de la configuration de l’authentification unique.
   
    b. Cliquez sur **Suivant**.

5. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

6. Dans une autre fenêtre de navigateur, connectez-vous à votre application Optimizely.

7. Cliquez sur le nom de votre compte dans l’angle supérieur droit, puis sur **Paramètres du compte**.
   
    ![Authentification unique Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

8. Sous l’onglet Compte, cochez la case **Activer l’authentification unique** sous Authentification unique dans la section **Vue d’ensemble**.
   
    ![Authentification unique Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.
Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.

6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-an-optimizely-test-user"></a>Création d’un utilisateur de test Optimizely
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Optimizely.

1. Sur la page d’accueil, sélectionnez l’onglet **Collaborateurs**

2. Cliquez sur **Nouveau collaborateur** pour ajouter un nouveau collaborateur au projet.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Renseignez l’adresse de messagerie et assignez un rôle au nouveau collaborateur. Cliquez sur **Inviter**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

1. Il reçoit une invitation par courrier électronique. À l’aide de l’adresse de messagerie, il doit se connecter à Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Optimizely.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Optimizely, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Classic, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Optimizely**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 

4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Optimizely dans le volet d’accès, vous devez être connecté automatiquement à votre application Optimizely.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

