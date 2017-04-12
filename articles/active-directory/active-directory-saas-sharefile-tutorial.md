---
title: "Didacticiel : Intégration d’Azure Active Directory à Citrix ShareFile | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ddf6c69b-4a76-4b42-8619-6f2a22800a23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9dfbf4bcdcd580773a368b90c869bf9c4fefbd77
ms.lasthandoff: 03/25/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Didacticiel : Intégration d’Azure Active Directory à Citrix ShareFile

Dans ce didacticiel, vous allez apprendre à intégrer Citrix ShareFile à Azure Active Directory (Azure AD).

L’intégration de Citrix ShareFile à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Citrix ShareFile.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Citrix ShareFile (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Citrix ShareFile, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Citrix ShareFile pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Citrix ShareFile à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-citrix-sharefile-from-the-gallery"></a>Ajout de Citrix ShareFile à partir de la galerie
Pour configurer l’intégration de Citrix ShareFile avec Azure AD, vous devez ajouter Citrix ShareFile, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Citrix ShareFile à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Citrix ShareFile**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_001.png)

5. Dans le volet de résultats, sélectionnez **Citrix ShareFile**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Citrix ShareFile avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Citrix ShareFile équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Citrix ShareFile associé doit être établi.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Citrix ShareFile.

Pour configurer et tester l’authentification unique Azure AD avec Citrix ShareFile, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Citrix ShareFile](#creating-a-citrix-sharefile-test-user)** pour avoir un équivalent de Britta Simon dans Citrix ShareFile, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail de gestion Azure et vous configurez l’authentification unique dans votre application Citrix ShareFile.

**Pour configurer l’authentification unique Azure AD avec Citrix ShareFile, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Citrix ShareFile**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_01.png)

3. Dans la section **Domaine et URL Citrix ShareFile**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_02.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenant-name>.sharefile.com/saml/login`    

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Si vous ne connaissez ces URL, tapez les exemples d’URL de l’exemple de modèle. Vous obtenez ensuite les URL réelles une fois que vous avez terminé l’étape 13.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_05.png) 

5. Dans la section **Configuration de Citrix ShareFile** , cliquez sur **Configurer Citrix ShareFile** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_06.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_07.png)

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Citrix ShareFile en tant qu’administrateur.

7. Dans la barre d’outils située en haut, cliquez sur **Admin**.

8. Dans le volet de navigation de gauche, sélectionnez **Configure Single Sign-On**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_002.png)

9. Dans la page de boîte de dialogue **Single Sign-On/ SAML 2.0 Configuration** sous **Basic Settings**, procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-sharefile-tutorial/sso_configuration_2.png)

    a. Cliquez sur **Enable SAML**.

    b. Dans la zone de texte **Your IDP Issuer/Entity ID**, copiez la valeur de **SAML Entity ID** indiquée dans la fenêtre Configuration de l’application Azure AD.

    c. Cliquez sur **Change** en regard du champ **X.509 Certificate**, puis chargez le certificat que vous avez téléchargé. 

    d. Dans la zone de texte **Login URL** (URL de connexion), copiez la valeur de **URL du service d’authentification unique SAML** indiquée dans la fenêtre Configuration de l’application Azure AD.

    e. Dans la zone de texte **Sign-out URL** (URL de déconnexion), copiez la valeur de **URL du service de déconnexion** indiquée dans la fenêtre Configuration de l’application Azure AD.

10. Cliquez sur **Save** dans le portail de gestion Citrix ShareFile.
    
 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-citrix-sharefile-test-user"></a>Création d’un utilisateur de test Citrix ShareFile

Pour permettre aux utilisateurs Azure AD de se connecter à Citrix ShareFile, vous devez les approvisionner dans Citrix ShareFile. En l’occurrence, cet approvisionnement est une tâche manuelle.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Citrix ShareFile en tant qu’administrateur.

2. Dans la barre d’outils située en haut, cliquez sur **Manage Users**. Accédez ensuite à **Manage Users Home** et cliquez sur **Create Employee**.

    ![Nouvel utilisateur](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_010.png "Nouvel utilisateur")

3. Dans la section **Basic Information**, procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_011.png "Nouvel utilisateur")

    a. Dans la zone de texte **Email Address** , tapez l’adresse de messagerie du compte de Britta Simon.  

    b. Dans la zone de texte **First Name**, tapez **Britta**.  

    c. Dans la zone de texte **Last Name**, tapez **Simon**.

4. Cliquez sur **Add User**.

    > [!NOTE]
    > Le détenteur du compte AAD recevra un message électronique et suivra un lien pour confirmer le compte avant qu’il ne soit activé. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Citrix ShareFile pour approvisionner des comptes d’utilisateur Azure Active Directory.



### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Citrix ShareFile.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Citrix ShareFile, suivez les étapes ci-dessous :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Citrix ShareFile**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Citrix ShareFile dans le volet d’accès, vous devez vous connecter automatiquement à votre application Citrix ShareFile.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
