---
title: "Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 2891838b56dd15cb5f97dcae391770143a80c781
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Atlassian Cloud à Azure Active Directory (Azure AD).

L’intégration d’Atlassian Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Atlassian Cloud
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Atlassian Cloud (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Atlassian Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Atlassian Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Atlassian Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Ajout d’Atlassian Cloud à partir de la galerie
Pour configurer l’intégration d’Atlassian Cloud à Azure AD, vous devez ajouter Atlassian Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Atlassian Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Atlassian Cloud**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_search.png)

5. Dans le panneau de résultats, sélectionnez **Atlassian Cloud**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Atlassian Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Atlassian Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Atlassian Cloud associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Atlassian Cloud.

Pour configurer et tester l’authentification unique avec Azure AD avec Atlassian Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Atlassian Cloud](#creating-an-atlassian-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Atlassian Cloud lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Atlassian Cloud.

**Pour configurer l’authentification unique Azure AD avec Atlassian Cloud, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Atlassian Cloud**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Dans la section **Domaine et URL Atlassian Cloud**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par IDP** :

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<instancename>.atlassian.net/admin/saml/edit`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://id.atlassian.com/login/saml/acs`

4. Si vous souhaitez configurer l’application en mode démarré par le **fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Vous pouvez obtenir les valeurs exactes dans l’écran de configuration SAML d’Atlassian Cloud.
 
5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration d’Atlassian Cloud**, cliquez sur **Configurer Atlassian Cloud**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

7. Pour configurer l’authentification unique de votre application, connectez-vous au portail Atlassian à l’aide des droits d’administrateur.

8. Dans la section Authentification de la barre de navigation gauche, cliquez sur **Domaines**.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    a. Dans la zone de texte, entrez votre nom de domaine, puis cliquez sur **Ajouter un domaine**.
        
    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. Pour vérifier le domaine, cliquez sur **Vérifier**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. Téléchargez le fichier html de vérification du domaine, chargez-le vers le dossier racine du site web de votre domaine, puis cliquez sur **Vérifier le domaine**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    d. Une fois le domaine vérifié, la valeur du champ **Status** (État) est **Verified** (Vérifié).

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

9. Dans la barre de navigation de gauche, cliquez sur **SAML**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

10. Créez une configuration SAML et ajoutez la configuration du fournisseur d’identité.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Dans la zone de texte **Identity Provider Entity ID** (ID d’entité du fournisseur d’identité), collez **l’ID d’entité SAML** que vous avez copié dans le portail Azure.

    b. Dans la zone de texte **Identity provider SSO URL** (URL d’authentification unique du fournisseur d’identité), collez **l’URL du service d’authentification unique SAML** que vous avez copiée dans le portail Azure.

    c. Ouvrez le certificat téléchargé dans le portail Azure, copiez les valeurs sans les lignes Begin et End, puis collez-les dans la zone **Certificat public X509**.
    
    d. Cliquez sur **Save Configuration** (Enregistrer la configuration) pour enregistrer les paramètres.
     
11. Mettez à jour les paramètres Azure AD pour avoir la certitude que vous avez configuré l’URL d’identificateur correcte.
  
    a. Copiez la valeur du champ **SP Entity ID** (ID d’entité du fournisseur d’identité) de l’écran SAML, puis collez-la dans Azure AD comme valeur de l’option **Identificateur**.

    b. L’URL d’authentification est l’URL du client de votre Atlassian Cloud.   

     ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
12. Dans le portail Azure, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-atlassian-cloud-test-user"></a>Création d’un utilisateur de test Atlassian Cloud

Pour permettre aux utilisateurs Azure AD de se connecter à Atlassian Cloud, vous devez les attribuer dans Atlassian Cloud.  
Pour Atlassian Cloud, cette attribution est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Dans la section Administration de site, cliquez sur le bouton **Users** (Utilisateurs).

    ![Créer un utilisateur Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Cliquez sur le bouton **Create User** (Créer un utilisateur) pour créer un utilisateur dans Atlassian Cloud.

    ![Créer un utilisateur Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Entrez l’adresse e-mail de l’utilisateur dans le champ **Email address**, son nom d’utilisateur dans **Username** et son nom complet dans **Full Name**, puis attribuez l’accès à l’application. 

    ![Créer un utilisateur Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Quand vous cliquez sur le bouton **Create user** (Créer l’utilisateur), un e-mail d’invitation est envoyé à l’utilisateur, et une fois l’invitation acceptée, l’utilisateur est actif dans le système. 

>[!NOTE] 
>Vous pouvez également créer des utilisateurs en bloc en cliquant sur le bouton **Créer en bloc** dans la section Utilisateurs.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Atlassian Cloud.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Atlassian Cloud, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Atlassian Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Atlassian Cloud dans le volet d’accès, vous devez être connecté automatiquement à votre application Atlassian Cloud. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

