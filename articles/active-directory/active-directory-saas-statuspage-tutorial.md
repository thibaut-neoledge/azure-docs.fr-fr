---
title: "Didacticiel : intégration d’Azure Active Directory à StatusPage | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et StatusPage."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 3b901bcb08a194f72c3dc75e33f2b94fffea370e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Didacticiel : Intégration d’Azure Active Directory à StatusPage
L’objectif de ce didacticiel est de vous montrer comment intégrer StatusPage dans Azure AD (Azure Active Directory).

L’intégration de StatusPage dans Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à StatusPage. 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à StatusPage via l’authentification unique (SSO) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec StatusPage, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement StatusPage pour lequel l’authentification unique (SSO) est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique (SSO) Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de StatusPage à partir de la galerie 
* Configuration et test de l’authentification unique (SSO) Azure AD

## <a name="add-statuspage-from-the-gallery"></a>Ajouter StatusPage à partir de la galerie
Pour configurer l’intégration de StatusPage avec Azure AD, vous devez ajouter StatusPage à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter StatusPage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **StatusPage**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)
7. Dans le volet des résultats, sélectionnez **StatusPage**, puis cliquez sur **Terminé** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique (SSO) Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique (SSO) Azure AD avec StatusPage au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique (SSO) fonctionne, Azure AD doit savoir qui est l’utilisateur StatusPage équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur StatusPage associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans StatusPage.

Pour configurer et tester l’authentification unique Azure AD avec StatusPage, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test StatusPage](#creating-a-statuspage-test-user)** pour avoir un équivalent de Britta Simon dans StatusPage qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique (SSO) Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application StatusPage. 

**Pour configurer l’authentification unique (SSO) Azure AD avec StatusPage, procédez comme suit :**

1. Dans le portail Azure Classic, puis sur la page d’intégration d’application **StatusPage**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à StatusPage**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 
   
   >[!NOTE]
   >Contactez l’équipe de support StatusPage à l’adresse [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)pour demander les métadonnées nécessaires à la configuration de l’authentification unique. 
   > 
  1. À partir des métadonnées, copiez la valeur de l’émetteur et collez-la dans la zone de texte **Identificateur** .
  2. À partir des métadonnées, copiez l’URL de réponse et collez-la dans la zone de texte **URL de réponse** .
  3. Cliquez sur **Suivant**.

4. Sur la page **Configurer l’authentification unique sur StatusPage** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png)   
  1. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
  2. Cliquez sur **Suivant**.
5. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.
6. Dans la barre d’outils principale, cliquez sur **Manage Account**(Gérer le compte).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 
7. Cliquez sur l’onglet **Single Sign-on** (Authentification unique). 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 
8. Sur la page de configuration de l’authentification unique, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png)  
  1. Dans le portail Azure Classic, sur la page **Configurer l’authentification unique sur StatusPage**, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL cible d’authentification unique**. 
  2. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat** . 
  3. Cliquez sur **Save**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
    ![Authentification unique Azure AD][10]
10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) 
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.  
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png)  
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
  1. Notez la valeur du **Nouveau mot de passe**  
  2. Cliquez sur **Terminé**.   

### <a name="create-a-statuspage-test-user"></a>Créer un utilisateur de test StatusPage
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans StatusPage.

StatusPage prend en charge l’approvisionnement juste-à-temps. Vous l’avez déjà activé dans [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

**Pour créer un utilisateur appelé Britta Simon dans StatusPage, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur **Manage Account**.
3. Cliquez sur l’onglet Team Members (Membres de l’équipe). 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 
4. Cliquez sur l’onglet **Add Team Member**(Ajouter un membre d’équipe). 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 
5. Entrez l’adresse électronique, le prénom et le nom de l’utilisateur valide que vous souhaitez configurer dans les zones de texte correspondantes, à savoir, **Email Address** (Adresse électronique), **First Name** (Prénom) et **Sur Name** (Nom). 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 
6. Pour **Role**, choisissez **Client Administrator**.
7. Cliquez sur **Create Account**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique (SSO) Azure en lui accordant l’accès à StatusPage.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à StatusPage, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **StatusPage**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette StatusPage dans le volet d’accès, vous devez être connecté automatiquement à votre application StatusPage.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png







