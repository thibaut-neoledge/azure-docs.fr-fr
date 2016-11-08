---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Halogen Software'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Halogen Software.
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
# <a name="tutorial:-azure-active-directory-integration-with-halogen-software"></a>Didacticiel : Intégration d’Azure Active Directory avec Halogen Software
L’objectif de ce didacticiel est de vous montrer comment intégrer Halogen Software dans Azure AD (Azure Active Directory).

L’intégration de Halogen Software dans Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à Halogen Software. 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Halogen Software (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Halogen Software, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Halogen Software pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Halogen Software à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-halogen-software-from-the-gallery"></a>Ajout de Halogen Software à partir de la galerie
Pour configurer l’intégration de Halogen Software avec Azure AD, vous devez ajouter Halogen Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Halogen Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page. 
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone Rechercher, entrez **halogen software**.
   
    ![Applications][5]
7. Dans le volet des résultats, sélectionnez **Halogen Software**, puis cliquez sur **Terminer** pour ajouter l’application.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Halogen Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Halogen Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Halogen Software associé doit être établi.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Halogen Software.

Pour configurer et tester l’authentification unique Azure AD avec Halogen Software, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Halogen Software](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Halogen Software qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Halogen Software.

**Pour configurer l’authentification unique Azure AD avec Halogen Software, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration de l’application **Halogen Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][8]
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Halogen Software ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][9]
3. Procédez comme suit dans la boîte de dialogue **Configurer les paramètres d’application** :  ![Configurer les paramètres d’application][10]
   
     a. Dans la zone de texte **URL de connexion**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application Halogen Software à au format suivant : *https://global.hgncloud.com/fabrikam/welcome.jsp*
   
     b. Cliquez sur **Next**.
4. Sur la page **Configurer l’authentification unique sur Halogen Software**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.
   
    ![Qu’est-ce qu’Azure AD Connect ?][11]
5. Dans une autre fenêtre de navigateur, connectez-vous à votre application **Halogen Software** en tant qu’administrateur.
6. Cliquez sur l’onglet **Options** . 
   
    ![Qu’est-ce qu’Azure AD Connect ?][12]
7. Dans le volet de navigation de gauche, cliquez sur **SAML Configuration**(Configuration SAML). 
   
    ![Qu’est-ce qu’Azure AD Connect ?][13]
8. Procédez comme suit dans la page **SAML Configuration** (Configuration SAML) :  ![Qu’est-ce qu’Azure AD Connect ?][14]
   
    a. Dans **Unique Identifier** (Identificateur unique), sélectionnez **NameID**.
   
    b. Dans **Unique Identifier Maps To** (l’identificateur unique mappe vers), sélectionnez **Username** (Nom d’utilisateur).
   
    c. Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Browse** (Parcourir) pour sélectionner le fichier, puis sur **Upload File** (Charger le fichier).
   
    d. Pour tester la configuration, cliquez sur **Run Test**(Exécuter le test). 
   
   > [!NOTE]
   > Vous devez attendre que le message « *The SAML test is complete. Please close this window* » s’affiche. Ensuite, fermez la fenêtre du navigateur. La case à cocher **Enable SAML** (Activer SAML) est sélectionnée uniquement si le test a été effectué.
   > 
   > 
   
    e. Sélectionnez **Enable SAML**.
   
    f. Cliquez sur **Enregistrer les modifications**. 
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**. 
   
    ![Qu’est-ce qu’Azure AD Connect ?][15]
10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Qu’est-ce qu’Azure AD Connect ?][16]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][100] 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][101] 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Qu’est-ce qu’Azure AD Connect ?][102] 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Qu’est-ce qu’Azure AD Connect ?][103] 
   
    a. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur Suivant.
6. Sur la page **Profil utilisateur** , procédez comme suit : 
   
   ![Qu’est-ce qu’Azure AD Connect ?][104] 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][105]  
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Qu’est-ce qu’Azure AD Connect ?][106]   
   
    a. Notez la valeur du **Nouveau mot de passe**.
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-halogen-software-test-user"></a>Création d’un utilisateur de test Halogen Software
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Halogen Software.

**Pour créer un utilisateur appelé Britta Simon dans Halogen Software, procédez comme suit :**

1. Connectez-vous à votre application **Halogen Software** en tant qu’administrateur.
2. Cliquez sur l’onglet **User Center** (Centre utilisateur), puis sur **Create User** (Créer un utilisateur).
   
    ![Qu’est-ce qu’Azure AD Connect ?][300]  
3. Dans la boîte de dialogue **New User** , procédez comme suit :
   
    ![Qu’est-ce qu’Azure AD Connect ?][301]
   
    a. Dans la zone de texte **First Name**, tapez **Britta**. 
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Username** (Nom d’utilisateur), entrez le **nom d’utilisateur de Britta Simon dans le portail Azure Classic**.
   
    d. Dans la zone **Password** , entrez un mot de passe pour Britta.
   
    e. Cliquez sur **Enregistrer**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Halogen Software.

![Qu’est-ce qu’Azure AD Connect ?][200]

**Pour attribuer Britta Simon à Halogen Software, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Qu’est-ce qu’Azure AD Connect ?][201]
2. Dans la liste des applications, sélectionnez **Halogen Software**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][202]
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][204]
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Halogen Software dans le volet d’accès, vous devez être connecté automatiquement à votre application Halogen Software.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png


<!--HONumber=Oct16_HO2-->


