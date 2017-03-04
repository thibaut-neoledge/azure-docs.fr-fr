---
title: "Didacticiel : Intégration d’Azure Active Directory à Convercent | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Convercent."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6bbfc7e22e5cb70ed1466bda14831412aead76a6
ms.openlocfilehash: bacabf7fe51e3c417a93a54f043c14aa5d65b31d
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Didacticiel : Intégration d’Azure Active Directory à Convercent

Dans ce didacticiel, vous allez apprendre à intégrer Convercent à Azure Active Directory (Azure AD).

L’intégration de Convercent à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Convercent.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Convercent (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le nouveau portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Convercent, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Convercent pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Convercent à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-convercent-from-the-gallery"></a>Ajout de Convercent à partir de la galerie
Pour configurer l’intégration de Convercent à Azure AD, vous devez ajouter Convercent à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Convercent à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Puis à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Convercent**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. Dans le volet des résultats, sélectionnez **Convercent**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Convercent avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Convercent équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Convercent associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Convercent.

Pour configurer et tester l’authentification unique Azure AD avec Convercent, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Convercent](#creating-a-works-mobile-test-user)** : pour avoir un équivalent de Britta Simon dans Convercent lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Convercent.

**Pour configurer l’authentification unique Azure AD avec Convercent, procédez comme suit :**

1. Dans le nouveau portail Azure, sur la page d’intégration de l’application **Convercent**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. Dans la section **Domaines et URL Convercent**, si vous souhaitez configurer l’application en **Mode initié par IDP**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)

    a. Dans la zone de texte **Identificateur**, tapez : `https://sts.convercent.com/`

    b. Cliquez sur **« Afficher les paramètres d’URL avancés »**

    c. Dans la zone de texte **Relay State** (État du relais), entrez : `https://app.convercent.com/`
    
4. Si vous souhaitez configurer l’application en **Mode initié par SP**, dans la section **Domaine et URL Convercent**, procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)

    a. Dans la zone de texte **URL d’authentification**, tapez : `https://app.convercent.com/`

    > [!NOTE] 
    > Ici, nous vous recommandons d’utiliser l’identificateur unique spécifié. Contactez l’[équipe de support Convercent](mailTo:support@convercent.com) pour obtenir cette valeur.

5. Dans la section **Configuration de Convercent** , cliquez sur **Configurer Convercent** pour ouvrir la boîte de dialogue **Configurer l’authentification**. Puis cliquez sur **SAML XML Metadate**(Métadonnées XML SAML) et enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’[équipe de support Convercent](mailTo:support@convercent.com) en lui fournissant les **métadonnées** téléchargées.

7. Dans le nouveau portail Azure, cliquez sur le bouton **Enregistrer**.  
  

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L'objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le nouveau portail.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-convercent-test-user"></a>Création d’un utilisateur de test Convercent

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Convercent. Collaborez avec [l’équipe de support Convercent](emailto:support@convercent.com) pour ajouter des utilisateurs dans la plateforme Convercent.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Convercent.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Convercent, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Convercent**.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Convercent dans le volet d’accès, vous devez être connecté automatiquement à votre application Convercent.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
