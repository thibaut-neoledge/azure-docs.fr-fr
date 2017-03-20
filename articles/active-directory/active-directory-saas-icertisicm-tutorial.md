---
title: "Didacticiel : Intégration d’Azure Active Directory avec Icertis Contract Management Platform | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Icertis Contract Management Platform."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 51c6f95585e2a3c00bfdfa660941ef010b20fe7d
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Didacticiel : Intégration d’Azure Active Directory avec Icertis Contract Management Platform
L’objectif de ce didacticiel est de vous montrer comment intégrer Icertis Contract Management Platform dans Azure AD (Azure Active Directory).

L’intégration d’Icertis Contract Management Platform avec Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès à Icertis Contract Management Platform.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Icertis Contract Management Platform via l’authentification unique avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Icertis Contract Management Platform, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Icertis Contract Management Platform pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir [un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Icertis Contract Management Platform depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-icertis-contract-management-platform-from-the-gallery"></a>Ajouter Icertis Contract Management Platform à partir de la galerie
Pour configurer l’intégration d’Icertis Contract Management Platform avec Azure AD, vous devez ajouter Icertis Contract Management Platform, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Icertis Contract Management Platform à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Icertis Contract Management Platform**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_01.png)
7. Dans le volet des résultats, sélectionnez **Icertis Contract Management Platform**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Icertis Contract Management Platform au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Icertis Contract Management Platform équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Icertis Contract Management Platform associé doit être établi.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Icertis Contract Management Platform.

Pour configurer et tester l’authentification unique Azure AD avec Icertis Contract Management Platform, vous devez suivre les indications des sections ci-après :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Icertis Contract Management Platform](#creating-a-icertis-contract-management-platform-test-user)** pour avoir un équivalent de Britta Simon dans Icertis Contract Management Platform lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Icertis Contract Management Platform.

**Pour configurer l’authentification unique Azure AD avec Icertis Contract Management Platform, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration de l’application **Icertis Contract Management Platform**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Icertis Contract Management Platform ?**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_03.png) 
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_04.png)
  1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.icertis.com`
  2. Cliquez sur **Suivant**.

   >[!NOTE]
   >Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez Icertis Contract Management Platform.
   >

1. Sur la page **Configurer l’authentification unique sur Icertis Contract Management Platform**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_05.png)
  1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
  2. Cliquez sur **Suivant**.
2. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Icertis Contract Management Platform et envoyez-lui les éléments suivants : 
   *  le fichier de **métadonnées téléchargé** ; 
   *   **l’ID d’entité** ; 
   *   **l’URL d’authentification unique SAML** ; 
   *   **l’URL du service de déconnexion unique**.
3. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_05.png)
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation. 
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_06.png)
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_08.png)
  1. Notez la valeur du **Nouveau mot de passe**. 
  2. Cliquez sur **Terminé**.   

### <a name="create-a-icertis-contract-management-platform-test-user"></a>Créer un utilisateur de test Icertis Contract Management Platform
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Icertis Contract Management Platform. Collaborez avec l’équipe du support technique Icertis Contract Management Platform pour ajouter des utilisateurs dans Icertis Contract Management Platform.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Icertis Contract Management Platform.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Icertis Contract Management Platform, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Icertis Contract Management Platform**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Icertis Contract Management Platform dans le volet d’accès, vous devez être connecté automatiquement à votre application Icertis Contract Management Platform.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_205.png

