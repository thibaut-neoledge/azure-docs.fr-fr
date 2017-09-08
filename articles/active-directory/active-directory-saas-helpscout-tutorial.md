---
title: "Didacticiel : Intégration d’Azure Active Directory à Help Scout | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Didacticiel : Intégration d’Azure Active Directory à Help Scout

Dans ce didacticiel, vous allez apprendre à intégrer Help Scout à Azure Active Directory (Azure AD).

L’intégration de Help Scout à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Help Scout.
- Vous pouvez connecter automatiquement vos utilisateurs à Help Scout à l’aide de l’authentification unique et de leur compte Azure AD.
- Vous pouvez gérer vos comptes en un seul et même endroit : le Portail Azure.

Pour en savoir plus sur l’intégration d’applications software as a service (SaaS) à Azure AD, consultez l’article [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Help Scout, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Help Scout, avec authentification unique activée 

> [!NOTE]
> Si vous testez les étapes de ce didacticiel, nous vous déconseillons d’utiliser un environnement de production.

Recommandations pour tester les étapes de ce didacticiel :

- N’utilisez pas votre environnement de production, à moins que cela ne soit nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez [obtenir un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajoutez Help Scout à partir de la galerie.
2. Configurez et testez l’authentification unique Azure AD.

## <a name="add-help-scout-from-the-gallery"></a>Ajouter Help Scout à partir de la galerie
Pour configurer l’intégration de Help Scout à Azure AD, ajoutez Help Scout à votre liste d’applications SaaS gérées à partir de la galerie.

Pour ajouter Help Scout à partir de la galerie :

1. Dans le menu gauche du [Portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Page Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Help Scout**. Dans les résultats de la recherche, sélectionnez **Help Scout**, puis **Ajouter**.

    ![Help Scout dans la liste des résultats](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Help Scout pour un utilisateur de test nommé *Britta Simon*.

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Azure AD équivalent dans Help Scout. Une relation entre un utilisateur Azure AD et l’utilisateur Help Scout associé doit être établie.

Pour établir la relation, affectez la valeur du **nom d’utilisateur** dans Azure AD à **Nom d’utilisateur** dans Help Scout.

Pour configurer et tester l’authentification unique Azure AD avec Help Scout, effectuez les tâches suivantes :

1. [Configurer l’authentification unique Azure AD](#set-up-azure-ad-single-sign-on). Configure un utilisateur afin qu’il puisse utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user). Teste l’authentification unique Azure AD avec l’utilisateur Britta Simon.
3. [Créer un utilisateur de test Help Scout](#create-a-help-scout-test-user). Crée un équivalent de Britta Simon dans Help Scout lié à la représentation Azure AD associée.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user). Configure Britta Simon afin qu’elle puisse utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on). Vérifie que la configuration fonctionne.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez configurer l’authentification unique Azure AD sur le Portail Azure. Ensuite, vous configurerez l’authentification unique dans votre application Help Scout.

Pour configurer l’authentification unique Azure AD avec Help Scout :

1. Sur la page d’intégration de l’application **Help Scout** du Portail Azure, sélectionnez **Authentification unique**.
 
    ![Lien Configurer l’authentification unique][4]

2. Sur la page **Authentification unique**, sélectionnez **Authentification SAML** comme **Mode**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Sous **Domaines et URL Help Scout**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par le fournisseur d’identité :

    1. Dans la zone **Identificateur**, entrez une URL au format suivant : `urn:auth0:helpscout:<instancename>`.

    2. Dans la zone **URL de réponse**, entrez une URL au format suivant : `https://helpscout.auth0.com/login/callback?connection=<instancename>`.

    ![Informations d’authentification unique dans Domaine et URL Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. Si vous voulez configurer l’application en mode initié par le fournisseur de services, cochez la case **Afficher les paramètres d’URL avancés**, puis suivez les étapes ci-dessous :

    * Dans la zone **URL d’authentification**, entrez une URL au format suivant : `https://secure.helpscout.net/members/login/`.

    ![Informations d’authentification unique dans Domaine et URL Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > Les valeurs de ces URL servent uniquement à des fins de démonstration. Mettez-les à jour avec les URL réelles d’identification et de réponse. Pour obtenir ces valeurs, contactez [l’équipe de support Help Scout](mailto:help@helpscout.com). 

5. Sous **Certificat de signature SAML**, sélectionnez **XML de métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Pour configurer l’authentification unique côté Help Scout, envoyez le XML de métadonnées téléchargé à [l’équipe de support Help Scout](mailto:help@helpscout.com). L’équipe de support Help Scout applique ce paramètre ; ainsi, la session d’authentification unique SAML est correctement configurée des deux côtés.

> [!TIP]
> Vous pouvez lire une version concise de ces instructions sur le [Portail Azure](https://portal.azure.com), pendant que vous configurez votre application ! Après avoir ajouté l’application en sélectionnant **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**. Vous pouvez accéder à la documentation incorporée dans la section **Configuration** en bas de la page. Pour plus d’informations, consultez la page [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon sur le Portail Azure.

![Créer un utilisateur de test Azure AD][100]

Pour créer un utilisateur de test dans Azure AD :

1. Dans le menu de gauche du Portail Azure, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, sélectionnez **Utilisateurs et groupes**, puis **Tous les utilisateurs**.

    ![Sélectionnez Utilisateurs et groupes, puis Tous les utilisateurs.](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur** en haut de la page **Tous les utilisateurs**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, suivez les étapes ci-dessous :

    1. Dans la zone **Nom**, entrez **BrittaSimon**.

    2. Dans la zone **Nom d’utilisateur**, entrez l’adresse e-mail de l’utilisateur Britta Simon.

    3. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    4. Sélectionnez **Créer**.

        ![Boîte de dialogue Utilisateur](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Créer un utilisateur de test Help Scout

L’objet de cette section est de créer un utilisateur nommé Britta Simon dans Help Scout. Help Scout prend en charge l’approvisionnement juste-à-temps (JIT), activé par défaut.

Dans cette section, il n’y a aucune action ni aucune tâche à effectuer. S’il n’y a pas déjà un utilisateur dans Help Scout, un nouvel utilisateur est créé lorsque vous tentez d’y accéder.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure AD en accordant au compte d’utilisateur l’accès à Help Scout.

![Attribuer le rôle utilisateur][200] 

Pour affecter Britta Simon à Help Scout :

1. Sur le Portail Azure, ouvrez l’affichage des applications, puis accédez à l’affichage des répertoires. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Help Scout**.

    ![Lien Help Scout dans la liste des applications](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien Utilisateurs et groupes][202]

4. Sélectionnez **Ajouter**. Ensuite, sur la page **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Sur la page **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Sur la page **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

7. Sur la page **Ajouter une affectation**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Vous devriez être automatiquement authentifié sur votre application Help Scout lorsque vous sélectionnez la vignette Help Scout dans le volet d’accès.

Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


