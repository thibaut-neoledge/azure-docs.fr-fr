---
title: "Didacticiel : Intégration d’Azure Active Directory à EasyTerritory | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et EasyTerritory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d29b362d-e986-4f67-8ff2-e158e49353aa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 77ab362fb0c00017f28c326cbe43f69eb39a3f76
ms.openlocfilehash: 95a6f808cdd0dd398949d22f95094e906331a1a7


---
# <a name="tutorial-azure-active-directory-integration-with-easyterritory"></a>Didacticiel : Intégration d’Azure Active Directory à EasyTerritory

Dans ce didacticiel, vous découvrez comment intégrer EasyTerritory à Azure Active Directory (Azure AD).

L’intégration d’EasyTerritory à Azure AD vous procure les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à EasyTerritory.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à EasyTerritory (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le Portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à EasyTerritory, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement EasyTerritory pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’EasyTerritory à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-easyterritory-from-the-gallery"></a>Ajout d’EasyTerritory à partir de la galerie
Pour configurer l’intégration d’EasyTerritory dans Azure AD, vous devez ajouter EasyTerritory à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter EasyTerritory à partir de la galerie, procédez comme suit :**

1. Dans le **[Portail de gestion Azure](https://portal.azure.com)**, dans le panneau de navigation gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **EasyTerritory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_001.png)

5. Dans le panneau des résultats, sélectionnez **EasyTerritory**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec EasyTerritory, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur EasyTerritory équivalent dans Azure AD. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et un utilisateur EasyTerritory associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans EasyTerritory.

Pour configurer et tester l’authentification unique Azure AD avec EasyTerritory, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur test EasyTerritory](#creating-an-easyterritory-test-user)** pour avoir un équivalent de Britta Simon dans EasyTerritory lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le Portail de gestion Azure et vous configurez l’authentification unique dans votre application EasyTerritory.

**Pour configurer l’authentification unique Azure AD avec EasyTerritory, procédez comme suit :**

1. Dans le Portail de gestion Azure, sur la page d’intégration de l’application **EasyTerritory**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_01.png)

3. Dans la section **Domaines et URL EasyTerritory**, si vous voulez configurer l’application en **Mode initié par IDP**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_02.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://apps.easyterritory.com/<tenant id>/DEV/`
    
    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://apps.easyterritory.com/<tenant id>/DEV/AuthServices/Acs`
    
4. Si vous voulez configurer l’application en **Mode initié par SP**, dans la section **Domaine et URL EasyTerritory**, procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_03.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.easyterritory.com/`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL d’authentification, l’identificateur et l’URL de réponse. Contactez [l’équipe de support technique EasyTerritory](mailto:sales@easyterritory.com) pour obtenir ces valeurs.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_04.png)     

6. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_300.png)

7. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_05.png)

8. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_400.png)

9. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_06.png) 

10. Pour obtenir la configuration de l’authentification unique pour votre application, contactez [l’équipe de support EasyTerritory](mailto:sales@easyterritory.com) en lui fournissant les **métadonnées** téléchargées. 


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-an-easyterritory-test-user"></a>Création d’un utilisateur de test EasyTerritory

Dans cette section, vous créez un utilisateur appelé Britta Simon dans EasyTerritory. Collaborez avec [l’équipe du support technique EasyTerritory](mailto:sales@easyterritory.com) pour ajouter les utilisateurs à la plateforme EasyTerritory.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EasyTerritory.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à EasyTerritory, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires et à **Applications d’entreprise** puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **EasyTerritory**.

    ![Configurer l’authentification unique](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette EasyTerritory dans le panneau d’accès, vous devez être connecté automatiquement à votre application EasyTerritory.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


