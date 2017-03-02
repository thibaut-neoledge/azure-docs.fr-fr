---
title: "Didacticiel : Intégration d’Azure Active Directory avec Bonusly | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Didacticiel : Intégration d’Azure Active Directory avec Bonusly

Dans ce didacticiel, vous allez apprendre à intégrer Bonusly avec Azure Active Directory (Azure AD).

L’intégration d’Azure AD avec Bonusly offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Bonusly.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Bonusly (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Bonusly, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Bonusly pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Bonusly à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-bonusly-from-the-gallery"></a>Ajout de Bonusly à partir de la galerie
Pour configurer l’intégration de Bonusly dans Azure AD, vous devez ajouter Bonusly à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Bonusly à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Puis à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Bonusly**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. Dans le volet de résultats, sélectionnez **Bonusly**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Bonusly avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Bonusly équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Bonusly associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Bonusly.

Pour configurer et tester l’authentification unique Azure AD avec Bonusly, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Bonusly](#creating-a-bonusly-test-user)** pour avoir un équivalent de Britta Simon dans Bonusly lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail de gestion Azure et configurer l’authentification unique dans votre application Bonusly.

**Pour configurer l’authentification unique Azure AD avec Bonusly, suivez les étapes ci-dessous :**

1. Sur la page d’intégration d’applications **Bonusly** du Portail de gestion Azure, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. Dans la section **Domaines et URL Bonusly**, si vous souhaitez configurer l’application en **Mode initié par IDP**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `bonusly`
    
    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://bonus.ly/saml/<APP-ID>/consume`
    
4. Si vous souhaitez configurer l’application en **Mode initié par SP**, dans la section **Domaine et URL Bonusly**, suivez les étapes ci-dessous :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://bonus.ly/saml/<your_subdomain>/consume`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Pour obtenir ces valeurs, contactez [l’équipe de support technique Bonusly](mailto:sales@easyterritory.com).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Puis cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. Dans la section **Configuration de Bonusly**, cliquez sur **Configurer Bonusly** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Bonusly en tant qu’administrateur.

12. Dans la barre d’outils située en haut, cliquez sur **Settings**, puis sélectionnez **Integrations and apps**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. Sous **Single Sign-On**, sélectionnez **SAML**.

14. Dans la page de boîte de dialogue **SAML** , procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    a. Dans la zone de texte **IdP SSO target URL**, copiez la valeur de **l’URL du service d’authentification unique SAML** indiquée dans la fenêtre de configuration de l’application Azure AD.

    b. Dans la zone de texte **IdP Login URL**, copiez la valeur de **l’URL du service d’authentification unique SAML** indiquée dans la fenêtre de configuration de l’application Azure AD.

    c. Dans la zone de texte **IdP Issuer**, copiez la valeur de **SAML Entity ID** indiquée dans la fenêtre de configuration de l’application Azure AD.

    d. Copiez la valeur d’empreinte du certificat téléchargé, puis collez-la dans la zone de texte **Cert Fingerprint**.

    e. Cliquez sur **Save**.

    > [!NOTE] 
    > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be).



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, tapez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-bonusly-test-user"></a>Création d’un utilisateur de test Bonusly

Pour permettre aux utilisateurs Azure AD de se connecter à Bonusly, vous devez les approvisionner dans Bonusly.
En l’occurrence, cet approvisionnement est une tâche manuelle.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Bonusly en tant qu’administrateur.

2. Cliquez sur **Settings**.

    ![Nouvel utilisateur](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "Nouvel utilisateur")

3. Cliquez sur l’onglet **Users and bonuses** .

    ![Nouvel utilisateur](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "Nouvel utilisateur")

4. Cliquez sur **Manage Users**.

    ![Nouvel utilisateur](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "Nouvel utilisateur")

5. Cliquez sur **Add User**.

    ![Nouvel utilisateur](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "Nouvel utilisateur")

6. Dans la section **Add User** , procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "Nouvel utilisateur")

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **E-mail** , tapez l’adresse de messagerie du compte de Britta Simon.

    d. Cliquez sur **Save**.

    > [!NOTE] 
    > Le détenteur du compte AAD recevra un message électronique contenant un lien à suivre pour confirmer le compte avant qu’il ne soit activé. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bonus.ly pour approvisionner des comptes d’utilisateur Azure Active Directory.



### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Bonusly.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Bonusly, suivez les étapes ci-dessous :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Bonusly**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Bonusly dans le volet d’accès vous connecte automatiquement à votre application Bonusly.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png
