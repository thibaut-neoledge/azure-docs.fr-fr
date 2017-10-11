---
title: "Didacticiel : Intégration d’Azure Active Directory à Teamwork | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Teamwork."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 03760032-3d76-4b47-ab84-241f72fbd561
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: edd2f9446515531f1147a8abf99295b618b89b25
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamwork"></a>Didacticiel : Intégration d’Azure Active Directory à Teamwork

Dans ce didacticiel, vous allez apprendre à intégrer Teamwork à Azure Active Directory (Azure AD).

L’intégration de Teamwork à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Teamwork.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Teamwork (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le Portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Teamwork, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Teamwork pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Teamwork à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-teamwork-from-the-gallery"></a>Ajout de Teamwork à partir de la galerie
Pour configurer l’intégration de Teamwork à Azure AD, vous devez ajouter Teamwork à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Teamwork à partir de la galerie, procédez comme suit :**

1. Dans le **[Portail de gestion Azure](https://portal.azure.com)**, dans le panneau de navigation gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Teamwork**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_001.png)

5. Dans le panneau de résultats, sélectionnez **Teamwork**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Teamwork au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Teamwork équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Teamwork associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** (Nom d’utilisateur) dans Teamwork.

Pour configurer et tester l’authentification unique Azure AD avec Teamwork, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Teamwork](#creating-a-teamwork-test-user)** pour avoir un équivalent de Britta Simon dans Teamwork lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail de gestion Azure et configurer l’authentification unique dans votre application Teamwork.

**Pour configurer l’authentification unique Azure AD avec Teamwork, procédez comme suit :**

1. Dans le Portail de gestion Azure, sur la page d’intégration de l’application **Teamwork**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_01.png)

3. Dans la section **Domaine et URL Teamwork**, renseignez la zone de texte **URL de connexion** avec une URL présentant le format suivant : `https://<company name>.teamwork.com`

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_02.png)

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique de Teamwork](mailto:support@teamwork.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_03.png)   

5. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_general_300.png)

6. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_04.png)

7. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_general_400.png)

8. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_05.png) 

9. Pour obtenir la configuration de l’authentification unique pour votre application, contactez [l’équipe de support technique de Teamwork](mailto:support@teamwork.com) en lui fournissant les **métadonnées** téléchargées.
  

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamwork-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamwork-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamwork-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-teamwork-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-teamwork-test-user"></a>Création d’un utilisateur de test Teamwork

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Teamwork. Collaborez avec [l’équipe de support technique de Teamwork](mailto:support@teamwork.com) pour ajouter les utilisateurs dans la plateforme Teamwork.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Teamwork.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Teamwork, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Teamwork**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamwork-tutorial/tutorial_teamwork_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Teamwork dans le volet d’accès, vous devez être connecté automatiquement à votre application Teamwork.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamwork-tutorial/tutorial_general_203.png