---
title: 'Didacticiel : Intégration d’Azure Active Directory à 8x8 Virtual Office | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et 8x8 Virtual Office.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-8x8-virtual-office"></a>Didacticiel : Intégration d’Azure Active Directory à 8x8 Virtual Office
L’objectif de ce didacticiel est de vous montrer comment intégrer 8x8 Virtual Office à Azure Active Directory (Azure AD).

L’intégration de 8x8 Virtual Office dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à 8x8 Virtual Office.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à 8x8 Virtual Office (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à 8x8 Virtual Office, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement 8x8 Virtual Office pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Microsoft Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de 8x8 Virtual Office à partir de la galerie
2. Configuration et test de l’authentification unique Microsoft Azure AD

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Ajout de 8x8 Virtual Office à partir de la galerie
Pour configurer l’intégration de 8x8 Virtual Office avec Azure AD, vous devez ajouter 8x8 Virtual Office, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter 8x8 Virtual Office à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, saisissez **8x8 Virtual Office**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. Dans le volet des résultats, sélectionnez **8x8 Virtual Office**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)

## <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Microsoft Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Microsoft Azure AD avec 8x8 Virtual Office au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur 8x8 Virtual Office équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur 8x8 Virtual Office associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans 8x8 Virtual Office.

Pour configurer et tester l’authentification unique Microsoft Azure AD avec 8x8 Virtual Office, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Microsoft Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)** pour avoir un équivalent de Britta Simon dans 8x8 Virtual Office lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Microsoft Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Microsoft Azure AD
Dans cette section, vous allez activer l’authentification unique Microsoft Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application 8x8 Virtual Office.

**Pour configurer l’authentification unique Microsoft Azure AD avec 8x8 Virtual Office, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **8x8 Virtual Office**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à 8x8 Virtual Office**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)
   
    a. Dans la zone de texte **URL de réponse**, tapez : `https://sso.8x8.com/saml2`
   
    b. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur 8x8 Virtual Office**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
5. Connectez-vous à votre client 8x8 Virtual Office en tant qu’administrateur.
6. Sélectionnez **Virtual Office Account Mgr** (Gestionnaire de compte Virtual Office) dans le volet Applications.
   
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)
7. Sélectionnez le compte **Entreprise** à gérer, puis cliquez sur le bouton **Se connecter**.
   
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)
8. Cliquez sur l’onglet **Comptes** dans la liste de menu.
   
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)
9. Cliquez sur **Authentification unique** dans la liste des comptes.
   
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)
10. Sélectionnez **Authentification unique** sous la section Méthode d’authentification et cliquez sur **SAML**.
    
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)
11. Copiez **l’URL d’authentification unique SAML**, **l’URL du service d’authentification unique** et **l’URL de l’émetteur** d’Azure AD dans les champs **URL de connexion**, **URL de déconnexion** et **URL de l’émetteur** de 8x8 Virtual Office. 
    
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
    ![Configurer l’authentification côté application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)
12. Cliquez sur le bouton **Explorateur** pour télécharger le certificat que vous avez téléchargé à partir d’Azure AD.
13. Cliquez sur le bouton **Enregistrer** .
14. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]
15. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Suivant**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-8x8-virtual-office-test-user"></a>Création d’un utilisateur de test 8x8 Virtual Office
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans 8x8 Virtual Office. 8x8 Virtual Office prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à 8x8 Virtual Office s’il n’existe pas déjà. 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique 8x8 Virtual Office.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à 8x8 Virtual Office.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à 8x8 Virtual Office, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **8x8 Virtual Office**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique avec Microsoft Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette 8x8 Virtual Office dans le volet d’accès, vous devez être connecté automatiquement à votre application 8x8 Virtual Office.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


