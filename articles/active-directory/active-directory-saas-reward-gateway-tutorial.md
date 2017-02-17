---
title: "Didacticiel : Intégration d’Azure Active Directory à Reward Gateway | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Reward Gateway."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8194e0d72bd8547595b15d5e1dcba5771d7b11c8


---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Didacticiel : Intégration d’Azure Active Directory à Reward Gateway
Dans ce didacticiel, vous allez apprendre à intégrer Reward Gateway à Azure Active Directory (Azure AD).

L’intégration de Reward Gateway à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Reward Gateway
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Reward Gateway (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Reward Gateway, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Reward Gateway pour lequel l’authentification unique est activée

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

1. Ajout de Reward Gateway à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-reward-gateway-from-the-gallery"></a>Ajout de Reward Gateway à partir de la galerie
Pour configurer l’intégration de Reward Gateway à Azure AD, vous devez ajouter Reward Gateway à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Reward Gateway à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Reward Gateway**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_01.png)
7. Dans le volet de résultats, sélectionnez **Reward Gateway**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Reward Gateway, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Reward Gateway équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Reward Gateway associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Reward Gateway.

Pour configurer et tester l’authentification unique Azure AD avec Reward Gateway, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Reward Gateway](#creating-a-reward-gateway-test-user)** pour avoir un équivalent de Britta Simon dans Reward Gateway lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Classic et configurer l’authentification unique dans votre application Reward Gateway.

**Pour configurer l’authentification unique Azure AD avec Reward Gateway, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration de l’application **Reward Gateway**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Reward Gateway ?**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_04.png) 
   
    a. Dans la zone de texte **URL d’identificateur**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Reward Gateway, au format suivant : 
   
   | URL d’identificateur |
   | --- |
   | `https://<company name>.rewardgateway.com/` |
   | `https://<company name>.rewardgateway.co.uk/` |
   | `https://<company name>.rewardgateway.co.nz/` |
   | `https://<company name>.rewardgateway.com.au/` |

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : 

    | URL de réponse |
    | --- |
    | `https://<company name>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |


    c. Cliquez sur **Suivant**

1. Sur la page **Configurer l’authentification unique sur Reward Gateway** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_05.png)
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
2. Pour configurer l’authentification unique pour votre application, contactez l’[équipe de support](mailTo:clientsupport@rewardgateway.com) Reward Gateway et fournissez les informations suivantes :
   
    • Les **métadonnées**
3. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-an-reward-gateway-test-user"></a>Création d’un utilisateur de test Reward Gateway
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Reward Gateway. Collaborez avec l’[équipe de support](mailTo:clientsupport@rewardgateway.com) Reward Gateway pour ajouter des utilisateurs à la plateforme Reward Gateway.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Reward Gateway.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Reward Gateway, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Reward Gateway**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Reward Gateway dans le panneau d’accès, vous devriez être connecté automatiquement à votre application Reward Gateway.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


