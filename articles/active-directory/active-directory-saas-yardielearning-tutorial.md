---
title: "Didacticiel : Intégration d’Azure Active Directory à Yardi eLearning | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Yardi eLearning."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7ea58b54-ec5b-4576-8586-814b11d0f4fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: ff98ac25a7f1ed9a7992477369642e037d44eca7
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-yardi-elearning"></a>Didacticiel : Intégration d’Azure Active Directory à Yardi eLearning
L’objectif de ce didacticiel est de vous montrer comment intégrer Yardi eLearning dans Azure AD (Azure Active Directory).

L’intégration de Yardi eLearning dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Yardi eLearning
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Yardi eLearning via l'authentification unique (SSO) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Yardi eLearning, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Yardi eLearning pour lequel l'authentification unique (SSO) est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de Yardi eLearning à partir de la galerie
* Configuration et test de l’authentification unique (SSO) Azure AD

## <a name="add-yardi-elearning-from-the-gallery"></a>Ajouter Yardi eLearning à partir de la galerie
Pour configurer l'intégration de Yardi eLearning à Azure AD, vous devez ajouter Yardi eLearning à votre liste d'applications SaaS gérées à partir de la galerie.

**Pour ajouter Yardi eLearning à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Yardi eLearning**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_01.png)
7. Dans le volet des résultats, sélectionnez **Yardi eLearning**, puis cliquez sur **Terminé** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
L'objectif de cette section est de vous montrer comment configurer et tester l'authentification unique (SSO) Azure AD avec Yardi eLearning avec un utilisateur de test appelé « Britta Simon ».

Pour que l'authentification unique (SSO) fonctionne, Azure AD doit savoir qui est l'utilisateur Yardi eLearning équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Yardi eLearning associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Yardi eLearning.

Pour configurer et tester l'authentification unique (SSO) Azure AD avec Yardi eLearning, vous devez suivre les blocs élémentaires suivants :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d'un utilisateur de test Yardi eLearning](#creating-a-yardi-elearning-test-user)** pour avoir un équivalent de Britta Simon dans Yardi eLearning lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique (SSO) Azure AD dans le portail Azure Classic et de configurer l’authentification unique (SSO) dans votre application Yardi eLearning.

**Pour configurer l'authentification unique Azure AD avec Yardi eLearning, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration de l’application **Yardi eLearning**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Yardi eLearning ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_04.png) 
  1. Dans la zone de texte URL de connexion, entrez l’URL dont vos utilisateurs se servent pour se connecter à votre application Yardi eLearning au format suivant : **« https://\<nom_entreprise\>.yardielearning.com/login »**.
  2. Cliquez sur **Suivant**.
4. Sur la page **Configurer l’authentification unique sur Yardi eLearning** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_05.png)   
   1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   2. Cliquez sur **Suivant**.
5. Pour configurer l’authentification unique pour votre application, contactez votre équipe de support Yardi eLearning via [elearning@yardi.com](mailto:elearning@yardi.com) et joignez le fichier de métadonnées téléchargé à votre courrier électronique.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_05.png) 
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_06.png) 
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.  
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_08.png) 
  1. Notez la valeur du **Nouveau mot de passe**.  
  2. Cliquez sur **Terminé**.   

### <a name="create-a-yardi-elearning-test-user"></a>Créer un utilisateur de test Yardi eLearning
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Yardi eLearning. Yardi eLearning prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé au moment d’une tentative d’accès à Yardi eLearning s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Yardi eLearning. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique (SSO) Azure en lui accordant l’accès à Yardi eLearning.

  ![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Yardi eLearning, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Yardi eLearning**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Yardi eLearning dans le volet d'accès, vous êtes connecté automatiquement à votre application Yardi eLearning.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_205.png

