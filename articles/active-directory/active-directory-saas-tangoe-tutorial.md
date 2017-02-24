---
title: "Didacticiel : intégration d’Azure Active Directory à Tangoe Command Premium Mobile | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tangoe Command Premium Mobile."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: bb140097831453d46f6bfef1c9fbe569eefb3020


---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Didacticiel : Intégration d’Azure Active Directory à Tangoe Command Premium Mobile
Dans ce didacticiel, vous allez apprendre à intégrer Tangoe Command Premium Mobile à Azure Active Directory (Azure AD).

L’intégration de Tangoe Command Premium Mobile à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Tangoe Command Premium Mobile.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Tangoe Command Premium Mobile (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central avec le portail Azure Classic

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Configuration requise
Pour configurer l’intégration d’Azure AD à Tangoe Command Premium Mobile, vous avez besoin des éléments suivants :

* Un abonnement Azure
* Un abonnement Tangoe Command Premium Mobile avec l’authentification unique activée

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

1. Ajout de Tangoe Command Premium Mobile à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-tangoe-command-premium-mobile-from-the-gallery"></a>Ajout de Tangoe Command Premium Mobile à partir de la galerie
Pour configurer l’intégration de Tangoe Command Premium Mobile à Azure AD, vous devez ajouter Tangoe Command Premium Mobile à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Tangoe Command Premium Mobile à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Tangoe Command Premium Mobile**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_01.png)
7. Dans le volet de résultats, sélectionnez **Tangoe Command Premium Mobile**, puis cliquez sur **Terminer** pour ajouter l’application.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Tangoe Command Premium Mobile pour un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Tangoe Command Premium Mobile équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Tangoe Command Premium Mobile associé doit être établie.

Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans Tangoe Command Premium Mobile.

Pour configurer et tester l’authentification unique Azure AD avec Tangoe Command Premium Mobile, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Tangoe Command Premium Mobile](#creating-an-tangoe-test-user)** pour avoir un équivalent de Britta Simon dans Tangoe Command Premium Mobile lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Classic et configurer l’authentification unique dans votre application Tangoe Command Premium Mobile.

**Pour configurer l’authentification unique Azure AD avec Tangoe Command Premium Mobile, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Tangoe Command Premium Mobile**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Tangoe Command Premium Mobile**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_04.png) 

   1. Dans la zone de texte **URL de connexion**, tapez l’URL qu’entrent vos utilisateurs pour se connecter à votre application Tangoe Command Premium Mobile en respectant le modèle suivant : **https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=\<émetteur du client\>&Target=\<URL de page cible\>**.

   2. Dans la zone de texte **URL de réponse**, tapez l’URL selon le modèle suivant : **https://sso.tangoe.com/sp/ACS.saml2**.

    > [!NOTE]  
    > Si vous ne connaissez pas les valeurs correctes pour les URL, vous pouvez utiliser les valeurs ci-dessus comme espaces réservés et demander les valeurs correctes au service clientèle Tangoe.
    >

4. Dans la page **Configurer l’authentification unique sur Tangoe Command Premium Mobile** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_05.png) 
   
   1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   2. Cliquez sur **Next**.

5. Pour configurer l’authentification unique pour votre application, contactez le service clientèle Tangoe en lui fournissant les éléments suivants :

   - le fichier de métadonnées téléchargé ;
   - l’ **URL de l’émetteur**
   - **L’URL d’authentification unique SAML**
   - l’ **URL du service d’authentification unique**

6. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

* Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_06.png) 
   
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Suivant**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_08.png) 
   
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

### <a name="creating-an-tangoe-command-premium-mobile-test-user"></a>Création d’un utilisateur de test Tangoe Command Premium Mobile
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Tangoe Command Premium Mobile. Tous les utilisateurs de Tangoe Command Premium Mobile doivent être configurés dans cette application avant de procéder à l’authentification unique. Par conséquent, appuyez-vous sur le service clientèle Tangoe pour configurer tous ces utilisateurs dans l’application. 

> [!NOTE]
> Si vous avez besoin de créer un utilisateur manuellement ou un groupe d’utilisateurs, vous devez contacter l’équipe de support Tangoe Command Premium Mobile.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tangoe Command Premium Mobile.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Tangoe Command Premium Mobile, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Classic, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.

 ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Tangoe Command Premium Mobile**.

 ![Configurer l’authentification unique](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_50.png) 
 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

 ![Affecter des utilisateurs][203] 

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à partir du volet d’accès.

Quand vous cliquez sur la vignette Tangoe Command Premium Mobile dans le volet d’accès, la connexion à votre application Tangoe Command Premium Mobile doit s’établir automatiquement.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


