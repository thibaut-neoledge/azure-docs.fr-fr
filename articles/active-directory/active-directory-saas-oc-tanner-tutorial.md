---
title: "Didacticiel : Intégration d’Azure Active Directory à O. C. Tanner - AppreciateHub | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et O. C. Tanner - AppreciateHub."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1b5527090a41c274b590ed9d1ac7b561b6f4ed14
ms.openlocfilehash: 10faa27e0c82e59c35e00258a2d90bea977dc28f


---
# <a name="tutorial-azure-active-directory-integration-with-o-c-tanner---appreciatehub"></a>Didacticiel : Intégration d’Azure Active Directory à O. C. Tanner - AppreciateHub.
L’objectif de ce didacticiel est de montrer comment intégrer O.C. Tanner - AppreciateHub à Azure Active Directory (Azure AD).  
L’intégration d’O.C. Tanner - AppreciateHub à Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à O.C. Tanner - AppreciateHub. 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à O.C. Tanner - AppreciateHub (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à O.C. Tanner - AppreciateHub, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement O.C. Un abonnement Tanner - AppreciateHub pour lequel l’authentification unique est activée

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

1. Ajout d’O.C. Tanner - AppreciateHub à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Ajout d’O.C. Tanner - AppreciateHub à partir de la galerie
Pour configurer l’intégration d’O.C. Tanner - AppreciateHub à Azure AD, vous devez ajouter O.C. Tanner - AppreciateHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter O.C. Tanner - AppreciateHub à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1] 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2] 
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3] 
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4] 
6. Dans la zone de recherche, tapez **O.C. Tanner - AppreciateHub**.
   
    ![Applications][5] 
7. Dans le volet de résultats, sélectionnez **O.C. Tanner - AppreciateHub**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Applications][25] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec O.C. Tanner - AppreciateHub au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur O.C. Tanner - AppreciateHub équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur O.C. Tanner - AppreciateHub associé doit être établie.  
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans O.C. Tanner - AppreciateHub.

Pour configurer et tester l’authentification unique Azure AD avec O.C. Tanner - AppreciateHub, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test O.C. Tanner - AppreciateHub](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans O.C. Tanner - AppreciateHub lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application O.C. Tanner - AppreciateHub.

**Pour configurer l’authentification unique Azure AD avec O.C. Tanner - AppreciateHub, procédez comme suit :**

1. Dans le Portail Azure Classic, sur la page **O.C. Tanner - AppreciateHub**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à O.C. Tanner - AppreciateHub**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][7]
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer les paramètres d’application][8]
   
     a. Ouvrez le fichier de métadonnées à l’aide du lien suivant : [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).
   
     b. Recherchez le nœud **md:AssertionConsumerService** . 
   
     c. Copiez la valeur de l’attribut **Location** . 
   
     ![Configurer les paramètres d’application][12]
   
     d. Dans la zone de texte **URL de connexion** , collez la valeur que vous avez obtenue à l’étape précédente.
   
    > [!NOTE]
    > Si vous ne parvenez pas à obtenir l’URL de réponse à partir du fichier de métadonnées, contactez l’équipe de support O.C. Tanner - AppreciateHub à l’adresse de messagerie [sso@octanner.com](mailto:sso@octanner.com).
    > 
    > 
   
    e. Cliquez sur **Suivant**.

4. Dans la page **Configurer l’authentification unique sur O.C. Tanner - AppreciateHub**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.
   
    ![Qu’est-ce qu’Azure AD Connect ?][9]
5. Contactez l’équipe de support O.C. Tanner - AppreciateHub à l’adresse de messagerie sso@octanner.com, fournissez-leur le fichier de métadonnées et demandez-leur d’activer l’authentification unique pour vous.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
    ![Qu’est-ce qu’Azure AD Connect ?][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Qu’est-ce qu’Azure AD Connect ?][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Création d’un utilisateur de test O.C. Tanner - AppreciateHub
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans O.C. Tanner - AppreciateHub.

**Pour créer un utilisateur nommé Britta Simon dans O.C. Tanner - AppreciateHub, procédez comme suit :**

Demandez à votre équipe de support OC Tanner de créer un utilisateur qui a comme attribut nameID le nom d’utilisateur de Britta Simon dans Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à O.C. Tanner - AppreciateHub.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à O.C. Tanner - AppreciateHub, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **O.C. Tanner - AppreciateHub**.
   
    ![Affecter des utilisateurs][202]
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Quand vous cliquez sur la vignette O.C. Tanner - AppreciateHub dans le volet d’accès, vous devez être connecté automatiquement à votre application O.C. Tanner - AppreciateHub.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png









<!--HONumber=Feb17_HO3-->


