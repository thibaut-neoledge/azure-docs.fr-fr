---
title: "Didacticiel : Intégration d’Azure Active Directory dans Asana | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Asana."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 583141e7543cb5e824204a49ff4400fe462ff28b


---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Didacticiel : Intégration d’Azure Active Directory dans Asana
Dans ce didacticiel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) dans Asana.

L’intégration d’Azure AD dans Asana offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Asana.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Asana (par authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Asana, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement **Asana** avec authentification unique activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Asana à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-asana-from-the-gallery"></a>Ajout d’Asana à partir de la galerie
Pour configurer l’intégration d’Asana dans Azure AD, vous devez ajouter Asana à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter Asana à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Asana**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)
7. Dans le volet de résultats, sélectionnez **Asana**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Asana, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Asana équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Asana associé doit être établie.
Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans Asana.

Pour configurer et tester l’authentification unique Azure AD avec Asana, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Asana](#creating-an-Asana-test-user)** pour avoir un équivalent de Britta Simon dans Asana, associé à sa représentation dans Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Asana.

**Pour configurer l’authentification unique Azure AD avec Asana, procédez comme suit :**

1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique][6]
2. Dans la page d’intégration d’applications **Asana** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][7] 
3. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Asana**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)
4. Sur la page **Configurer les paramètres d’application** , procédez comme suit : 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)

    a. Dans la zone de texte URL de connexion, tapez une URL au format suivant : `https://app.asana.com`

    c. Cliquez sur **Next**.

1. Dans la page **Configurer l’authentification unique sur Asana**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. Copiez également l’URL d’authentification unique SAML.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)
2. Cliquez avec le bouton droit sur le certificat, puis ouvrez le fichier dans le bloc-notes ou votre éditeur de texte préféré. Copiez le contenu entre le début et la fin du certificat. C’est ce certificat X.509 que vous allez utiliser dans Asana pour configurer l’authentification unique.
3. Dans une autre fenêtre de navigateur, connectez-vous à votre application Asana. Pour configurer l’authentification unique dans Asana, accédez aux paramètres de l’espace de travail en cliquant sur son nom dans le coin supérieur droit de l’écran. Ensuite, cliquez sur le **\<nom de votre espace de travail\> Paramètres**. 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)
4. Dans la fenêtre **Paramètres de l’organisation**, cliquez sur **Administration**. Ensuite, cliquez sur **Members must log in via SAML** (Les membres doivent se connecter via SAML) pour configurer l’authentification unique. Puis, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)
   
    a. Dans la zone de texte **URL de la page de connexion** , collez l’URL de connexion SAML à partir d’Azure AD.
   
    b. Dans la zone de texte **Certificat X.509** , collez le certificat X.509 que vous avez copié dans Azure AD.
5. Cliquez sur **Save**. Consultez le [Guide Asana pour configurer l’authentification unique](https://asana.com/guide/help/premium/authentication#gl-saml) si vous avez besoin d’aide.
6. Dans Azure AD, accédez à la page **Configurer l’authentification unique sur Asana**, sélectionnez la confirmation, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-an-asana-test-user"></a>Création d’un utilisateur de test Asana
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Asana.

1. Dans **Asana**, accédez à la section **Teams** (Équipes) dans le volet gauche. Cliquez sur le signe plus. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 
2. Tapez le message électronique britta.simon@contoso.com dans la zone de texte, puis sélectionnez **Invite**(Inviter).
3. Cliquez sur **Send Invite**(Envoyer l’invitation). Le nouvel utilisateur recevra un message électronique dans sa messagerie. Elle devra créer le compte et le valider.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Asana.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Asana, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Asana**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD.

Accédez à la page de connexion à Asana. Dans la zone correspondante, insérez l’adresse de messagerie britta.simon@contoso.com. Laissez la zone du mot de passe vide, puis cliquez sur **Connexion**. Vous êtes redirigé vers la page de connexion à Azure AD. Entrez vos informations d’identification Azure AD. Vous êtes maintenant connecté à Asana.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


