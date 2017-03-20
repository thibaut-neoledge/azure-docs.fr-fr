---
title: "Tutorial: Intégration d&quot;Azure Active Directory à Moxtra | Microsoft Docs"
description: "Découvrez comment configurer l&quot;authentification unique entre Azure Active Directory et Moxtra."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: cbb93424c6315ec7605bb238fd40dc62ccbb4e17
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Didacticiel : Intégration d'Azure Active Directory avec Moxtra
L’objectif de ce didacticiel est de vous montrer comment intégrer Moxtra dans Azure AD (Azure Active Directory).

L’intégration de Moxtra dans Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à Moxtra 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Moxtra via l’authentification unique (SSO) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l'intégration d'Azure AD avec Moxtra, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Moxtra pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Moxtra à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-moxtra-from-the-gallery"></a>Ajout de Moxtra à partir de la galerie
Pour configurer l'intégration de Moxtra à Azure AD, vous devez ajouter Moxtra à votre liste d'applications SaaS gérées à partir de la galerie.

**Pour ajouter Moxtra à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Moxtra**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)
7. Dans le volet de résultats, sélectionnez **Moxtra**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)

## <a name="configuring-and-testing-azure-ad-sso"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Moxtra avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Moxtra équivalent dans Azure AD. En d'autres termes, il faut établir une relation entre l'utilisateur Azure AD et l'utilisateur Moxtra associé.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans Moxtra.

Pour configurer et tester l'authentification unique Azure AD avec Moxtra, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d'un utilisateur de test Moxtra](#creating-a-moxtra-test-user)** pour avoir un équivalent de Britta Simon dans Moxtra lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de la configurer dans votre application Moxtra. 

Votre application Moxtra attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d'attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) 

**Pour configurer l'authentification unique Azure AD avec Moxtra, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Moxtra**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Moxtra**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) 
3. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) 
  1. Dans la zone de texte **URL d’authentification**, saisissez l’URL suivante : **https://www.moxtra.com/service/#login**. 
  2. Cliquez sur **Suivant**.
4. Sur la page **Configurer l'authentification unique sur Moxtra** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) 
  1. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
  2. Cliquez sur **Suivant**.
5. Dans une autre fenêtre de navigateur, connectez-vous à votre site d'entreprise Moxtra en tant qu'administrateur.
6. Dans la barre d’outils située à gauche, cliquez sur **Console Administrateur > Authentification unique SAML**, puis sur **Nouveau**.
   
  ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 
7. Sur la page **SAML** , procédez comme suit :
   
  ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
  1. Dans la zone de texte **Nom**, saisissez le nom de votre configuration (par ex., *SAML*). 
  2. Sur la page **Configurer l’authentification unique sur Moxtra** du portail Azure Classic, copiez la valeur **ID d’entité**, puis collez-la dans la zone de texte **ID d’entité IdP**. 
  3. Sur la page **Configurer l’authentification unique sur Moxtra** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion**. 
  4. Dans la zone de texte **AuthnContextClassRef**, saisissez **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
  5. Sur la page **Configurer l’authentification unique sur Moxtra** du portail Azure Classic, copiez la valeur **Format de l’identification du nom**, puis collez-la dans la zone de texte **Format du NameID**. 
  6. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat** .    
  7. Dans la zone de texte du domaine de messagerie SAML, tapez votre domaine de messagerie SAML.    
   
   >[!NOTE]
   >Pour connaître les étapes de vérification du domaine, cliquez sur le «**i**» ci-dessous.
   >  
  8. Cliquez sur **Update**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
  ![Authentification unique Azure AD][10]
9. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
  
  ![Authentification unique Azure AD][11]
19. Pour ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML, cliquez sur **Attributs** dans le menu supérieur pour ouvrir la boîte de dialogue **Attributs du jeton SAML**. 
   
  ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) 
11. Pour chaque ligne de données dans le tableau ci-dessous, procédez comme suit :
   
     | Nom de l'attribut | Valeur de l’attribut |
     | --- | --- |
     | firstname |givenname |
     | lastname |surname |
     | idpid |*\<la valeur **ID d’entité** renseignée dans la boîte de dialogue **Configurer l’authentification unique sur Moxtra** du portail Azure Classic \>* |
  1. Cliquez sur Ajouter un attribut utilisateur 

     ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) 
  2. Dans la boîte de dialogue **Ajouter un attribut utilisateur** , tapez le nom et la valeur de l'attribut indiqués pour cette ligne dans le tableau. 

     ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) 
  3. Cliquez sur **Terminé**.
12. Cliquez sur **Appliquer les modifications**. 
   
  ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png)  
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png)  
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) 
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) 
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

### <a name="create-a-moxtra-test-user"></a>Créer un utilisateur de test Moxtra
L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Moxtra.

**Pour créer un utilisateur appelé Britta Simon dans Moxtra, procédez comme suit :**

1. Connectez-vous à votre site d'entreprise Moxtra en tant qu'administrateur.
2. Dans la barre d’outils située à gauche, cliquez sur **Console Administrateur > Gestion des utilisateurs**, puis sur **Ajouter un utilisateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 
3. Dans la boîte de dialogue **Ajouter un utilisateur** , procédez comme suit :
  1. Dans la zone de texte **First Name**, tapez **Britta**.
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Email** , tapez l’adresse de messagerie de Britta dans le portail Azure Classic.
  4. Dans la zone de texte **Division**, saisissez **Développement**.
  5. Dans la zone de texte **Service**, saisissez **Informatique**.
  6. Sélectionnez **Administrateur**.
  7. Cliquez sur **Ajouter**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Moxtra.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Moxtra, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Moxtra**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Lorsque vous cliquez sur la mosaïque Moxtra dans le volet d'accès, vous êtes connecté automatiquement à votre application Moxtra.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png







