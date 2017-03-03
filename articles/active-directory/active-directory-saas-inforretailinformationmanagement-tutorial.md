---
title: "Didacticiel : Intégration d’Azure Active Directory avec Infor Retail – Information Management | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Infor Retail – Information Management."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ad9c78483af88a85a1b790269231683155760d76
ms.openlocfilehash: aef560c2b05a80c59743c4cc6d68d6d2c14bdc3f
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Didacticiel : Intégration d’Azure Active Directory avec Infor Retail – Information Management

Dans ce didacticiel, vous allez apprendre à intégrer Infor Retail – Information Management dans Azure Active Directory (Azure AD).

L’intégration d’Infor Retail – Information Management dans Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Infor Retail – Information Management.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Infor Retail – Information Management (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans Infor Retail – Information Management, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement autorisant l’authentification unique à Infor Retail – Information Management


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Infor Retail – Information Management à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Ajout d’Infor Retail – Information Management à partir de la galerie
Pour configurer l’intégration d’Infor Retail – Information Management dans Azure AD, vous devez ajouter Infor Retail – Information Management à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter Infor Retail – Information Management à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Puis à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Infor Retail – Information Management**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_001.png)

5. Dans le volet de résultats, sélectionnez **Infor Retail – Information Management**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Infor Retail – Information Management et un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Infor Retail – Information Management correspondant dans Azure AD. En d’autres termes, il faut établir un lien entre un utilisateur d’Azure AD et l’utilisateur correspondant dans Infor Retail – Information Management.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Infor Retail – Information Management.

Pour configurer et tester l’authentification unique Azure AD avec Infor Retail – Information Management, vous devez suivre les étapes suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Infor Retail – Information Management](#creating-an-infor-retail---information-management-test-user)** pour associer un équivalent de Britta Simon dans Infor Retail – Information Management à sa représentation dans Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Infor Retail – Information Management.

**Pour configurer l’authentification unique Azure AD avec Infor Retail – Information Management, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Infor Retail – Information Management**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_01.png)

3. Dans la section **Domaines et URL Infor Retail – Information Management**, si vous souhaitez configurer l’application en **Mode initié par IDP**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_02.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.mingle.infor.com`
    
    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.mingle.infor.com/sp/ACS.saml2`
    
4. Si vous souhaitez configurer l’application en **Mode initié par SP**, dans la section **Domaine et URL Infor Retail – Information Management**, procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_03.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.mingle.infor.com/<company code>`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Contactez [Équipe de support Infor Retail – Information Management](mailto:innovate@infor.com) pour obtenir ces valeurs.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_04.png)     

6. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Puis cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_300.png)

7. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_05.png)

8. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_400.png)

9. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_06.png) 

10. Pour configurer l’authentification automatique pour votre application, contactez l’[équipe de support d’Infor Retail – Information Management](mailto:innovate@infor.com) et fournissez-lui le fichier de **métadonnées** téléchargé.
  

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, tapez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-an-infor-retail--information-management-test-user"></a>Création d’un utilisateur de test Infor Retail – Information Management

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans Infor Retail – Information Management. Travaillez avec [l’équipe de support d’Infor Retail – Information Management](mailto:innovate@infor.com) pour ajouter des utilisateurs dans la plateforme Infor Retail – Information Management.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Infor Retail – Information Management.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Infor Retail – Information Management, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires et accédez à **Applications d’entreprise** puis à **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Infor Retail – Information Management**.

    ![Configurer l’authentification unique](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Infor Retail – Information Management dans le volet d’accès, vous devez vous connecter automatiquement à votre application Infor Retail – Information Management.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_203.png
