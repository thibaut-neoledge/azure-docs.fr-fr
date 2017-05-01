---
title: "Didacticiel : Intégration d’Azure Active Directory à WORKS MOBILE | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et WORKS MOBILE."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8d1b3a49f15861d886822fa1a7328301e97ce37e
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Didacticiel : Intégration d’Azure Active Directory à WORKS MOBILE

Dans ce didacticiel, vous découvrez comment intégrer WORKS MOBILE à Azure Active Directory (Azure AD).

L’intégration de WORKS MOBILE à Azure AD vous procure les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à WORKS MOBILE.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à WORKS MOBILE (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à WORKS MOBILE, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement WORKS MOBILE pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
>

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de WORKS MOBILE à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-works-mobile-from-the-gallery"></a>Ajouter WORKS MOBILE à partir de la galerie
Pour configurer l’intégration de WORKS MOBILE dans Azure AD, vous devez ajouter WORKS MOBILE à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter WORKS MOBILE à partir de la galerie, procédez comme suit :**

1. Dans le **[Portail de gestion Azure](https://portal.azure.com)**, dans le panneau de navigation gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]
2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]
4. Dans la zone de recherche, tapez **WORKS MOBILE**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_001.png)

5. Dans le volet de résultats, sélectionnez **WORKS MOBILE**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec WORKS MOBILE sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur WORKS MOBILE équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur WORKS MOBILE associé doit être établie.

Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans WORKS MOBILE.

Pour configurer et tester l’authentification unique Azure AD avec WORKS MOBILE, vous devez suivre les indications des sections ci-après :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test WORKS MOBILE](#creating-a-works-mobile-test-user)** pour avoir un équivalent de Britta Simon dans WORKS MOBILE qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail de gestion Azure et vous configurez l’authentification unique dans votre application WORKS MOBILE.

**Pour configurer l’authentification unique Azure AD avec WORKS MOBILE, procédez comme suit :**

1. Dans le Portail de gestion Azure, sur la page d’intégration de l’application **WORKS MOBILE**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]
2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_01.png)
3. Dans la section **Domaine et URL WORKS MOBILE**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_02.png)
  1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<your-subdomain>.worksmobile.com/jp/myservice`
  2. Dans la zone de texte **Identificateur**, entrez la valeur `worksmobile.com`.

    >[!NOTE] 
    >Il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Pour obtenir ces valeurs, contactez [l’équipe de support technique WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com). 
    >
    >

4. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_03.png)     
5. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_300.png)
6. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_04.png)
7. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)
8. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_05.png) 

9. Dans la section **Configuration de WORKS MOBILE** , cliquez sur **Configurer WORKS MOBILE** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_06.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_07.png)

10. Pour configurer l’authentification unique pour votre application, contactez votre [équipe de support technique WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) et indiquez-leur les informations suivantes : •  **Fichier de certificat téléchargé** •  **URL du service d’authentification unique SAML** •  **ID d’entité SAML** •  **URL de déconnexion**
  
### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 
2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 
3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 
4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 
  1. Dans la zone de texte **Nom**, entrez **BrittaSimon**.
  2. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.
  3. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.
  4. Cliquez sur **Create**. 

### <a name="create-a-works-mobile-test-user"></a>Créer un utilisateur de test WORKS MOBILE

Dans cette section, vous créez un utilisateur appelé Britta Simon dans WORKS MOBILE. Collaborez avec [l’équipe du support technique WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) pour ajouter des utilisateurs dans la plateforme WORKS MOBILE.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique (SSO) Azure en lui accordant l’accès à WORKS MOBILE.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à WORKS MOBILE, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires et à **Applications d’entreprise** puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **WORKS MOBILE**.

    ![Configurer l’authentification unique](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.
6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.
7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette WORKS MOBILE dans le panneau d’accès, vous devez être connecté automatiquement à votre application WORKS MOBILE.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png
