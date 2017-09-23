---
title: "Didacticiel : Intégration d’Azure Active Directory avec Pingboard | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pingboard."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 008c670a8043da0c67ccefde48d5ef721c75d97c
ms.contentlocale: fr-fr
ms.lasthandoff: 04/07/2017

---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Didacticiel : Intégration d’Azure Active Directory avec Pingboard

Ce didacticiel explique comment intégrer Pingboard avec Azure Active Directory (Azure AD).

L’intégration de Pingboard avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Pingboard
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Pingboard (via l’authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Pingboard, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Pingboard pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Pingboard à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-pingboard-from-the-gallery"></a>Ajout de Pingboard à partir de la galerie
Pour configurer l’intégration de Pingboard avec Azure AD, vous devez ajouter Pingboard, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Pingboard à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans le champ de recherche, tapez **Pingboard**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_search.png)

5. Dans le volet de résultats, sélectionnez **Pingboard**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Pingboard sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Pingboard équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Pingboard associé.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Pingboard.

Pour configurer et tester l’authentification unique Azure AD avec Pingboard, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Pingboard](#creating-a-pingboard-test-user)** pour avoir un équivalent de Britta Simon dans Pingboard lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Pingboard.

**Pour configurer l’authentification unique Azure AD auprès de Pingboard, procédez comme suit :**

1. Dans le portail de gestion Azure, dans la page d’intégration de l’application **Pingboard**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Dans la section **Domaines et URL Pingboard**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP**:

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `http://<entity-id>.pingboard.com/sp`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<entity-id>.pingboard.com/auth/saml/consume`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Pour obtenir ces valeurs, contactez l’[équipe de support technique Pingboard](https://support.pingboard.com/). 

4. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

    a. Dans la zone de texte **URL de connexion**, tapez la valeur suivante : `http://<sub-domain>.pingboard.com/sign_in`
     
5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique côté Pingboard, ouvrez une nouvelle fenêtre de navigateur et connectez-vous à votre compte Pingboard. Pour configurer l’authentification unique, vous devez être un administrateur Pingboard.

8. Dans le menu supérieur, sélectionnez **Applications > Intégrations**

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  Sur la page **Intégrations**, recherchez la mosaïque **« Azure Active Directory »**, puis cliquez dessus.

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. Dans la fenêtre modale qui suit, cliquez sur **« Configurer »**

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. Sur la page suivante, vous remarquerez que « l’intégration Azure SSO est activée ». Ouvrez le fichier XML de métadonnées téléchargé dans un bloc-notes puis collez le contenu dans **IDP Metadata** (métadonnées du fournisseur d’identité).

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. Le fichier sera validé et, si tout est correct, l’authentification unique sera alors activée

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-pingboard-test-user"></a>Création d’un utilisateur de test Pingboard

Pour permettre aux utilisateurs Azure AD de se connecter à Pingboard, vous devez les approvisionner dans Pingboard.  
Dans le cas de Pingboard, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Pingboard en tant qu’administrateur.

2. Cliquez sur le bouton **« Ajouter un employé »** sur la page **Annuaire**.

    ![Ajouter un employé](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. Dans la boîte de dialogue **Ajouter un employé** , procédez comme suit.

    ![Inviter des personnes](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. Dans la zone de texte **Nom complet**, tapez Britta Simon.

    b. Dans la zone de texte **E-mail** , tapez l’adresse de messagerie du compte de Britta Simon.

    c. Dans la zone de texte **Fonction**, tapez la fonction de Britta Simon.

    d. Dans la liste déroulante **Emplacement**, sélectionnez l’emplacement de Britta Simon.
    
    e. Cliquez sur **Add**.   

4. Un écran de confirmation s’affichera pour confirmer l’ajout de l’utilisateur.
    
    ![Confirmer](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pingboard.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Pingboard, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Pingboard**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Pingboard dans le volet d’accès, vous devez être connecté automatiquement à votre application Pingboard.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png

