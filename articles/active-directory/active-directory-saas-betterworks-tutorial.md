---
title: "Didacticiel : Intégration d’Azure Active Directory à BetterWorks | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et BetterWorks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c66450fd735b3d9e4d0f7835c6d607368007388


---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Didacticiel : Intégration d’Azure Active Directory à BetterWorks
L’objectif de ce didacticiel est de vous montrer comment intégrer BetterWorks dans Azure AD (Azure Active Directory).

L’intégration de BetterWorks dans Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à BetterWorks.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à BetterWorks (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Configuration requise
Pour configurer l’intégration d’Azure AD avec BetterWorks, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement BetterWorks pour lequel l’authentification unique est activée

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

1. Ajout de BetterWorks à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-betterworks-from-the-gallery"></a>Ajout de BetterWorks à partir de la galerie
Pour configurer l’intégration de BetterWorks avec Azure AD, vous devez ajouter BetterWorks à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter BetterWorks à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **BetterWorks**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)
7. Dans le volet de résultats, sélectionnez **BetterWorks**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec BetterWorks avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur BetterWorks équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur BetterWorks associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans BetterWorks.

Pour configurer et tester l’authentification unique Azure AD avec BetterWorks, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test BetterWorks](#creating-a-betterworks-test-user)** pour avoir un équivalent de Britta Simon dans BetterWorks lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application BetterWorks.

L’application BetterWorks attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet**Attribut**de l’application. La capture d’écran suivante montre un exemple : 

![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Pour configurer l’authentification unique Azure AD avec BetterWorks, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **BetterWorks**, dans le menu situé en haut, cliquez sur **Attributs**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)
2. Dans la boîte de dialogue **Attributs du jeton SAML** , pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    a. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)

    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur de l’attribut** , tapez l’ID du jeton saml indiqué pour cette ligne.

    d. Cliquez sur **Terminé**

1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à BetterWorks**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)

    a. Dans la zone de texte **Identificateur** , entrez l’URL au format suivant : `https://app.betterworks.com/saml2/metadata/`


    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://app.betterworks.com/saml2/acs/`


    c. Cliquez sur **Suivant**

1. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous voulez configurer l’application en **mode lancé par le fournisseur de service**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)
   
    a.    Sélectionnez **Affichez les paramètres avancés (facultatif)**.

    b. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application BetterWorks, au format suivant : `https://app.betterworks.com`

    b. Cliquez sur **Suivant**.

1. Dans la page **Configurer l’authentification unique sur BetterWorks**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
2. Pour que l’authentification unique soit configurée pour votre application, contactez l’équipe du support technique BetterWorks à l’adresse <mailto:support@betterworks.com>. Joignez le fichier de métadonnées téléchargé et partagez-le avec l’équipe BetterWorks pour qu’elle configure l’authentification unique de son côté.
3. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
   ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-betterworks-test-user"></a>Création d’un utilisateur de test BetterWorks
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans BetterWorks. 

Veuillez contacter l’équipe de support BetterWorks à l’adresse <mailto:support@betterworks.com> pour ajouter les utilisateurs sur la plateforme BetterWorks.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à BetterWorks.

   ![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à BetterWorks, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Classic, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **BetterWorks**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette BetterWorks dans le volet d’accès, vous devez être connecté automatiquement à votre application BetterWorks.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


