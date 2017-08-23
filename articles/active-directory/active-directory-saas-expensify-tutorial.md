---
title: "Didacticiel : Intégration d’Azure Active Directory avec Expensify | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Expensify."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e45576fd92706881121469ccd82150b3d48059cd
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Didacticiel : Intégration d’Azure Active Directory avec Expensify

Dans ce didacticiel, vous allez apprendre à intégrer Expensify avec Azure Active Directory (Azure AD).

L’intégration d’Expensify avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Expensify.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Expensify (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Expensify, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Expensify pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Expensify à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-expensify-from-the-gallery"></a>Ajout d’Expensify à partir de la galerie
Pour configurer l’intégration de Expensify à Azure AD, vous devez ajouter Expensify à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Expensify à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Expensify**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_search.png)

5. Dans le panneau de résultats, sélectionnez **Expensify**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Expensify avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Expensify équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Expensify associé doit être établie.

Dans Expensify, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Expensify, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Expensify](#creating-an-expensify-test-user)** pour avoir un équivalent de Britta Simon dans Expensify lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Expensify.

**Pour configurer l’authentification unique Azure AD avec Expensify, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Expensify**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_samlbase.png)

3. Dans la section **Expensify Domain and URLs** (Domaine et URL Expensify), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.expensify.com/authentication/saml/login`

    b. Dans la zone de texte **Identifier URL** (URL d’identificateur), tapez une URL au format suivant :  `https://www.<companyname>.expensify.com/` 

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’URL d’identificateur réelles. Pour obtenir ces valeurs, contactez [l’équipe de support technique Expensify](mailto:help@expensify.com). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-expensify-tutorial/tutorial_general_400.png)

6. Pour activer l’authentification unique dans Expensify, vous devez d’abord activer le **contrôle de domaine** dans l’application. Vous pouvez activer le contrôle de domaine dans l’application via la procédure répertoriée [ici](http://help.expensify.com/domain-control). Pour une assistance supplémentaire, travaillez avec [l’équipe de support technique Expensify](mailto:help@expensify.com). Une fois que le contrôle de domaine est activé, suivez les étapes ci-dessous :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_51.png)
    
    a. Connectez-vous à votre application Expensify.
    
    b. Dans la barre d’outils située en haut, cliquez sur **Admin**.
    
    c. Cliquez sur **Domaine**dans le panneau de gauche.
    
    d. Cliquez sur le nom de votre domaine vérifié.
    
    e. Dans le volet de gauche, cliquez sur **SAML**, puis sur **Activé**.
    
    f. Dans le Bloc-notes, ouvrez les métadonnées de fédération téléchargées à partir d’Azure AD, copiez le contenu et collez-le dans la zone de texte **Identity Provider Metadata** (Métadonnées du fournisseur d’identité).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-expensify-test-user"></a>Création d’un utilisateur test Expensify

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Expensify. Travaillez avec [l’équipe de support technique Expensify](mailto:help@expensify.com) pour ajouter les utilisateurs à la plateforme Expensify.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Expensify.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Expensify, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Expensify**.

    ![Configurer l’authentification unique](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Lorsque vous cliquez sur la mosaïque Expensify dans le volet d’accès, vous devriez être connecté automatiquement à votre application Expensify.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_203.png


