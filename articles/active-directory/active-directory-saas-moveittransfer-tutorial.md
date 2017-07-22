---
title: "Didacticiel : Intégration d’Azure Active Directory à MOVEit Transfer | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et MOVEit Transfer."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: e1b4cda400205611a957c23d9a9b90c918d260f3
ms.contentlocale: fr-fr
ms.lasthandoff: 02/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer"></a>Didacticiel : Intégration d’Azure AD à MOVEit Transfer
L’objectif de ce didacticiel est de vous montrer comment intégrer MOVEit Transfer à Azure Active Directory (Azure AD).

L’intégration de MOVEit Transfer à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à MOVEit Transfer
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à MOVEit Transfer via l’authentification unique (SSO) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration de MOVEit Transfer à Azure AD, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement MOVEit Transfer pour lequel l’authentification unique est activée

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

1. Ajout de MOVEit Transfer à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-moveit-transfer-from-the-gallery"></a>Ajout de MOVEit Transfer à partir de la galerie
Pour configurer l’intégration de MOVEit Transfer avec Azure AD, vous devez ajouter MOVEit Transfer, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter MOVEit Transfer à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **MOVEit Transfer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)
7. Dans le volet des résultats, sélectionnez **MOVEit Transfer**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec MOVEit Transfer, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur MOVEit Transfer équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur MOVEit Transfer associé.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans MOVEit Transfer.

Pour configurer et tester l’authentification unique Azure AD avec MOVEit Transfer, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test MOVEit Transfer](#creating-a-moveit-transfer-test-user)** pour avoir un équivalent de Britta Simon dans MOVEit Transfer lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-sso"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et la configurer dans votre application MOVEit Transfer.

**Pour configurer l’authentification unique Azure AD avec MOVEit Transfer, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration d’applications **MOVEit Transfer**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à MOVEit Transfer**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)
  1. Dans la zone de texte **URL de connexion** , entrez l’URL de connexion avec votre propre domaine.
  2. Dans la zone de texte **Identificateur** , tapez l’URL de l’ID d’entité.
  3. Dans la zone de texte **URL de réponse** , tapez une URL de l’interface Assertion Consumer.
  4. Cliquez sur **Suivant**.
   
   >[!NOTE]
   >Notez que vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, vous pouvez vous référer à l’étape 8 pour plus d’informations ou contacter [MOVEit Transfer](https://www.ipswitch.com/support/technical-support).
   >  
4. Dans la page **Configurer l’authentification unique sur MOVEit Transfer**, procédez comme suit et cliquez sur **Suivant** :
   
  ![Configurer l’authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)  
  1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
  2. Cliquez sur **Suivant**.
5. Connectez-vous à votre client MOVEit Transfer en tant qu’administrateur.
6. Dans le volet de navigation gauche, cliquez sur **Paramètres**.
   
  ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)
7. Cliquez sur le lien **Single Signon** (Authentification unique) qui se trouve sous **Security Policies -> User Auth** (Stratégies de sécurité -> Authentification de l’utilisateur).
   
  ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)
8. Cliquez sur le lien URL de métadonnées pour télécharger le document de métadonnées.
   
  ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)   
  * Vérifiez que le paramètre **entityID** correspond à **l’identificateur** de l’étape 3.
  * Vérifiez que l’URL de l’emplacement **AssertionConsumerService** correspond à **l’URL de réponse** de l’étape 3.
     
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)
9. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité) pour ajouter un nouveau fournisseur d’identité fédéré.
   
  ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)
10. Cliquez sur **Parcourir...** pour sélectionner le fichier de métadonnées que vous avez téléchargé à l’étape 4, puis cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité) pour charger le fichier téléchargé. 
    
  ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)
11. Sélectionnez **Oui** sous **Activé** dans la page **Edit Federated Identity Provider Settings** (Modifier les paramètres du fournisseur d’identité fédéré...), puis cliquez sur **Enregistrer**.
    
   ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)
12. Dans la page **Edit Federated Identity Provider User Settings** (Modifier les paramètres utilisateur du fournisseur d’identité fédéré), effectuez les actions suivantes, puis cliquez sur **Enregistrer**.   
  1. Sélectionnez **SAML NameID** comme **Nom de connexion**. 
  2. Sélectionnez **Autres** comme **Nom complet** et dans la zone de texte **Nom de l’attribut**, insérez la valeur : http://schemas.microsoft.com/identity/claims/displayname. 
  3. Sélectionnez **Autres** comme **Email** et dans la zone de texte **Nom de l’attribut**, insérez la valeur : http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress. 
  4. Sélectionnez **Oui** sous **Auto-create account on signon** (Créer automatiquement un compte lors de l’authentification). 
  5. Cliquez sur le bouton **Enregistrer** .
 
   ![Configurer l’authentification unique côté application](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
13. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]
14. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
   ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

### <a name="create-a-moveit-transfer-test-user"></a>Créer un utilisateur de test MOVEit Transfer
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans MOVEit Transfer. MOVEit Transfer prend en charge l’approvisionnement juste-à-temps que vous avez activé.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à MOVEit Transfer s’il n’existe pas déjà.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique MOVEit Transfer.
> 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à MOVEit Transfer.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à MOVEit Transfer, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **MOVEit Transfer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque MOVEit Transfer dans le volet d’accès, vous êtes connecté automatiquement à votre application MOVEit Transfer.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png

