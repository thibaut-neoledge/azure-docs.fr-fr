---
title: "Didacticiel : Intégration d’Azure Active Directory à Yonyx Interactive Guides | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Yonyx Interactive Guides."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 0d2ac97c7f8e42f1e6b847e338d0676592054d93
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Didacticiel : Intégration d’Azure Active Directory à Yonyx Interactive Guides
L’objectif de ce didacticiel est de vous montrer comment intégrer Yonyx Interactive Guides dans Azure AD (Azure Active Directory).

L’intégration de Yonyx Interactive Guides à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Yonyx Interactive Guides
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Yonyx Interactive Guides par le biais de l’authentification unique (SSO) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Yonyx Interactive Guides, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Yonyx Interactive Guides pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique (SSO) Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Yonyx Interactive Guides à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Ajout de Yonyx Interactive Guides à partir de la galerie
Pour configurer l’intégration de Yonyx Interactive Guides à Azure AD, vous devez ajouter Yonyx Interactive Guides à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Yonyx Interactive Guides à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Yonyx Interactive Guides**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_01.png)
7. Dans le volet des résultats, sélectionnez **Yonyx Interactive Guides**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
Cette section vous indique comment configurer et tester l’authentification unique Azure AD avec Yonyx Interactive Guides au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Yonyx Interactive Guides équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Yonyx Interactive Guides associé.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Yonyx Interactive Guides.

Pour configurer et tester l’authentification unique Azure AD avec Yonyx Interactive Guides, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Yonyx Interactive Guides](#creating-a-yonyx-interactive-guides-test-user)** pour avoir un équivalent de Britta Simon dans Yonyx Interactive Guides lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Classic et configurer l’authentification unique dans votre application Yonyx Interactive Guides.

**Pour configurer l’authentification unique Azure AD avec Yonyx Interactive Guides, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration de l’application **Yonyx Interactive Guides**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Yonyx Interactive Guides**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_04.png)
  1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`. 
  2. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.yonyx.com`.
  3. Cliquez sur **Suivant**.
   
    >[!NOTE]
    > Notez que vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’équipe de support Yonyx Interactive Guides via <mailto:support@yonyx.com>. 
    > 
4. Dans la page **Configurer l’authentification unique sur Yonyx Interactive Guides**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_05.png)
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Yonyx Interactive Guides à l’adresse <mailto:support@yonyx.com> , en lui fournissant les éléments suivants :
  * Le **certificat** téléchargé
  * **L’URL de l’émetteur**
  * **L’URL du service d’authentification unique**
  * l’ **URL du service d’authentification unique**
6. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_05.png) 
 1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.  
 2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**. 
 3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_06.png) 
 1. Dans la zone de texte **First Name**, tapez **Britta**.   
 2. Dans la zone de texte **Last Name**, tapez **Simon**. 
 3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**. 
 4. Dans la liste **Rôle**, sélectionnez **Utilisateur**. 
 5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_08.png) 
 1. Notez la valeur du **Nouveau mot de passe**.
 2. Cliquez sur **Terminé**.   

### <a name="create-a-yonyx-interactive-guides-test-user"></a>Créer un utilisateur de test Yonyx Interactive Guides
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Yonyx Interactive Guides. Yonyx Interactive Guides prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Yonyx Interactive Guides s’il n’existe pas déjà.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’équipe de support Yonyx Interactive Guides à l’adresse <mailto:support@yonyx.com>. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Yonyx Interactive Guides.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Yonyx Interactive Guides, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Yonyx Interactive Guides**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Yonyx Interactive Guides dans le volet d’accès, vous devez vous connecter automatiquement à votre application Yonyx Interactive Guides.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_205.png

