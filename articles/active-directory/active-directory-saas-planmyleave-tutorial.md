---
title: "Didacticiel : Intégration d’Azure Active Directory à PlanMyLeave | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et PlanMyLeave."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83334b1b02df214e51c86a862636c9392cd19474
ms.openlocfilehash: ba418a641b339a0d94a3c7b2596d37fbd88a30c5
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Didacticiel : Intégration de PlanMyLeave à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer PlanMyLeave à Azure Active Directory (Azure AD).

L’intégration de PlanMyLeave à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à PlanMyLeave.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à PlanMyLeave (par le biais de l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans PlanMyLeave, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement PlanMyLeave pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de PlanMyLeave depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-planmyleave-from-the-gallery"></a>Ajout de PlanMyLeave depuis la galerie
Pour configurer l’intégration de PlanMyLeave à Azure AD, vous devez ajouter PlanMyLeave depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter PlanMyLeave à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Puis à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans le champ de recherche, entrez **PlanMyLeave**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_001.png)

5. Dans le volet de résultats, sélectionnez **PlanMyLeave**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec PlanMyLeave avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur PlanMyLeave équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur PlanMyLeave associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans PlanMyLeave.

Pour configurer et tester l’authentification unique Azure AD avec PlanMyLeave, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test PlanMyLeave](#creating-a-planmyleave-test-user)** pour avoir un équivalent de Britta Simon dans PlanMyLeave qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application PlanMyLeave.

**Pour configurer l’authentification unique Azure AD avec PlanMyLeave, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **PlanMyLeave**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)

3. Dans la section **Domaine et URL PlanMyLeave**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_02.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company-name>.planmyleave.com/Login.aspx`
    
    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez [l’équipe de support technique PlanMyLeave](mailto:support@planmyleave.com) pour obtenir ces valeurs.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png)     

5. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Puis cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_300.png)

6. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png)

7. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

8. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png) 

9. Dans la section **Configuration de PlanMyLeave** , cliquez sur **Configurer PlanMyLeave** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_06.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_07.png)

10. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire PlanMyLeave en tant qu’administrateur.

11. Accédez à la section **System Setup** (Configuration système). Puis, dans la section **Security Management** (Gestion de la sécurité), cliquez sur **Company SAML settings** (Paramètres SAML d’entreprise).

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

12. Dans la section **SAML Settings** (Paramètres SAML), cliquez sur icône de l’éditeur.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

13. Dans la section **Update SAML Settings** (Mettre à jour les paramètres SAML), procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  Dans la zone de texte **Login URL** (URL de connexion), copiez la valeur de **URL du service d’authentification unique SAML** indiquée dans la fenêtre Configuration de l’application Azure AD.

    b.  Ouvrez votre fichier de certificat téléchargé dans le Bloc-notes, copiez uniquement le contenu compris entre ---Begin Certificate--- et ---End certificate---- dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat**.

    c. Définissez "**Is Enable**" sur "**Yes**".

    d. Cliquez sur **Save**.



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, tapez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-planmyleave-test-user"></a>Création d’un utilisateur de test PlanMyLeave

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans PlanMyLeave. PlanMyLeave prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à PlanMyLeave s’il n’existe pas déjà.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique PlanMyLeave](mailto:support@planmyleave.com).



### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à PlanMyLeave.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à PlanMyLeave, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **PlanMyLeave**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette PlanMyLeave dans le volet d’accès, vous êtes connecté automatiquement à votre application PlanMyLeave.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
