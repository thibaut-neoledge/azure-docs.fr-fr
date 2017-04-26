---
title: "Didacticiel : Intégration d’Azure Active Directory à Slack | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: a1d7c2f01d3bf345f63077c4d42d79476235e941
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Didacticiel : Intégration d’Azure AD avec Slack

Dans ce didacticiel, vous allez apprendre à intégrer Slack à Azure Active Directory (Azure AD).

Lorsque vous intégrez Slack à Azure AD, vous pouvez :

* Contrôler qui a accès à Slack.
* Autoriser l’authentification unique (SSO) à Slack des utilisateurs avec leurs comptes Azure AD.
* Gérer vos comptes à un emplacement central, le portail Azure.

Pour en savoir plus sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez l’article [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Slack, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Slack dans lequel l’authentification unique (SSO) est incluse

>[!NOTE]
>Nous déconseillons le test des étapes de ce didacticiel à l’aide d’un environnement de production.
>
>

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

* N’utilisez votre environnement de production que si nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, obtenez [un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique (SSO) Azure AD dans un environnement de test. Le scénario à suivre se compose de deux sections principales :

* Ajout de Slack à partir de la galerie
* Configuration et test de l’authentification unique (SSO) Azure AD

## <a name="add-slack-from-the-gallery"></a>Ajouter Slack à partir de la galerie
Pour configurer l’intégration de Slack avec Azure AD, ajoutez Slack à partir de la galerie à votre liste d’applications SaaS gérées en procédant comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Dans le volet gauche, cliquez sur le bouton **Azure Active Directory**.

    ![Le bouton « Azure Active Directory »][1]

3. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Le bouton « Applications d’entreprise » dans le panneau « Toutes les applications »][2]

4. En haut de la boîte de dialogue **toutes les applications**, cliquez sur **Ajouter**.

    ![Le bouton « Ajouter » dans la boîte de dialogue « Toutes les applications »][3]

5. Dans la zone de recherche, tapez **Slack**.

    ![La zone de recherche « Ajouter une application »](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. Dans le volet de résultats, sélectionnez **Slack**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Sélectionner « Slack » dans le volet de résultats](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique (SSO) Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique (SSO) Azure AD en utilisant l’utilisateur de test « Britta Simon ».

Pour l’authentification unique (SSO) fonctionne, Azure AD doit établir une relation de liaison entre l’utilisateur Azure AD et son équivalent dans Slack. Vous établissez cette relation de liaison en affectant la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Slack.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Slack, suivez les indications des sections ci-après :

1. [Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on) pour permettre à l’utilisateur d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user) pour tester l’authentification unique (SSO) Azure AD avec l’utilisateur Britta Simon.
3. [Créer un utilisateur de test Slack](#creating-a-slack-test-user) pour attribuer à l’utilisateur Azure AD Britta Simon un équivalent Slack auquel le lier.
4. [Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user) pour permettre à l’utilisateur Britta Simon d’utiliser l’authentification unique (SSO) Azure AD.
5. [Tester l’authentification unique](#testing-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique (SSO) Azure AD dans le portail Azure et la configurer dans votre application Slack en procédant comme suit :

1. Dans le portail Azure, sur la page d’intégration de l’application **Slack**, cliquez sur **Authentification unique**.

    ![La page d’intégration de l’application Slack][4]

2. Dans la boîte de dialogue **Authentification unique**, dans la liste **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique (SSO).

    ![La boîte de dialogue « Authentification unique »](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. Sous **Domaine et URL Slack**, procédez comme suit :

    ![La boîte de dialogue « Authentification unique »](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)
  1. Dans la zone **URL de connexion**, saisissez une URL qui utilise la convention d’affectation de noms _https://<company name>.slack.com_.
  2. Dans la zone **Identificateur** , tapez **https://slack.com**.

     >[!NOTE]
     >Les valeurs ci-dessus ne sont pas des valeurs réelles. Nous vous recommandons d’utiliser ici des valeurs uniques pour l’URL et l’identificateur. Vous mettrez à jour les valeurs ultérieurement avec l’URL et l’identificateur réels. Pour obtenir les valeurs, contactez l’[équipe de support Slack](https://slack.com/help/contact).
     >
     >

4. L’application Slack attend les assertions Security Assertion Markup Language (SAML) à afficher dans un format spécifique. Configurez les revendications et gérez les valeurs des attributs dans la section **Attributs utilisateur** de la page d’intégration de l’application Slack, comme illustré dans la capture d’écran suivante :

    ![Configurer les revendications dans la section Attributs utilisateur](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. Dans la boîte de dialogue **Authentification unique**, dans la section **Attributs utilisateur**, sélectionnez **user.mail** en tant qu’**Identificateur utilisateur**. Pour chaque ligne dans le tableau, procédez comme suit :

    | Nom de l’attribut | Valeur de l’attribut |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    1. Cliquez sur le bouton **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    2. Dans la boîte de dialogue **Ajouter un attribut**, dans la zone **Nom**, entrez le premier nom dans la colonne **Nom de l’attribut** du tableau.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    3. Dans la zone **Valeur**, entrez la première valeur dans la colonne **Valeur de l’attribut** du tableau.
    4. Cliquez sur **OK**.
    5. Répétez les étapes « a » à « d » pour les trois lignes suivantes du tableau.

6. Sous **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Création d’un certificat](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. Dans la boîte de dialogue **Créer un certificat**, cliquez sur le bouton **Calendrier**n sélectionnez une date d’expiration, puis cliquez sur **Enregistrer**.

    ![Sélectionner une date d'expiration du certificat](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. Sous **Certificat de signature SAML**, cochez la case **Activer le nouveau certificat**, puis cliquez sur **Enregistrer**.

    ![Activer le certificat de signature SAML](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![La fenêtre contextuelle Certificat de substitution](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. Sous **Certificat de signature SAML**, cliquez sur **Certificat (base64)**, puis enregistrez le fichier du certificat sur votre disque local.

    ![Enregistrer le certificat sur votre disque local](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. Sous **Configuration de Slack**, cliquez sur **Configurer Slack** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Cliquer sur « Configurer Slack » pour ouvrir la fenêtre Configurer l’authentification](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![La fenêtre « Configurer l’authentification »](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. Ouvrez une nouvelle fenêtre de navigateur, puis connectez-vous au site de votre entreprise Slack en tant qu’administrateur.

13. Accédez à **Microsoft Azure AD**, puis à **Team Settings**.

    ![Le bouton « Team Settings » de Microsoft Azure AD sur le site d’entreprise Slack](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. Sous **Team Settings**, cliquez sur l’onglet **Authentication**, puis sur **Change Settings**.

    ![Le bouton « Change Settings » sur la page Team Settings](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![La boîte de dialogue « SAML Authentication Settings »](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)
  1. Dans la zone **SAML 2.0 Endpoint (HTTP)**, entrez la valeur de la zone **SAML Single Sign-On Service URL** indiquée dans la fenêtre Configuration de l’application Azure AD.
  2. Dans la zone **Identity Provider Issuer**, entrez la valeur de **SAML Entity ID** indiquée dans la fenêtre Configuration de l’application Azure AD.
  3. Ouvrez le fichier de certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone **Certificat public**.
  4. Configurez les trois paramètres précédents comme nécessaire pour votre équipe Slack. Pour plus d’informations sur les paramètres, consultez le [Guide de l’authentification unique avec Slack](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack).
  5. Cliquez sur **Enregistrer la configuration**.
  6. Décochez la case **Autoriser les utilisateurs à modifier leur adresse de messagerie**.
  7. Cochez la case **Autoriser les utilisateurs à choisir leur propre nom d’utilisateur**.
  8. Dans **L’authentification de votre équipe doit être utilisée par**, sélectionnez **C’est facultatif**.
  
### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Azure en procédant comme suit :

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Le bouton « Azure Active Directory »](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.

    ![Le bouton « Tous les utilisateurs » Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. En haut de la boîte de dialogue **Tous les utilisateurs**, cliquez sur **Ajouter**.

    ![Le bouton « Ajouter » dans la boîte de dialogue Ajouter des utilisateurs](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, entrez les informations suivantes :

    ![La boîte de dialogue « Utilisateur »](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)
  1. Dans la zone **Nom**, tapez **BrittaSimon**.
  2. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.
  3. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans la zone **Mot de passe**.
  4. Cliquez sur **Create**.

### <a name="create-a-slack-test-user"></a>Créer un utilisateur de test Slack

Dans cette section, un utilisateur appelé Britta Simon est créé dans Slack. Slack prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Slack, un nouveau est créé lorsque vous tentez d’accéder à Slack.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support Slack](https://slack.com/help/contact).
>
>

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Slack.

![Affecter un utilisateur de l’authentification unique Azure][200]

Pour affecter l’utilisateur Britta Simon à Slack, procédez comme suit :

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste **Applications**, sélectionnez **Slack**.

    ![La liste des applications du portail Azure](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. Dans le volet gauche, cliquez sur **Utilisateurs et groupes**.

    ![Bouton « Utilisateurs et groupes » du volet gauche][202]

4. Cliquez sur le bouton **Ajouter** puis, dans le panneau **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Le bouton « Ajouter » et le panneau « Ajouter une affectation »][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, dans la liste **Utilisateurs**, sélectionnez **Britta Simon**.

6. Cliquez sur le bouton **Sélectionner**.

7. Dans le panneau **Ajouter une affectation**, cliquez sur le bouton **Affecter**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Pour tester la configuration, accédez au volet d’accès, puis cliquez sur la vignette **Slack**. L’utilisateur doit être automatiquement connecté à l’application Slack.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

