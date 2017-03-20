---
title: "Didacticiel : Intégration d’Azure Active Directory à MaxxPoint | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et MaxxPoint."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d92e3edb-31aa-4d21-9bf8-6e5917d7bf5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6844a46978618d5dc3292168bbcc228480311d47
ms.openlocfilehash: e84a3aeb9e7a8cddccb293ab2bb9d7fdc5fa82b4
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-maxxpoint"></a>Didacticiel : Intégration d’Azure Active Directory à MaxxPoint

Dans ce didacticiel, vous allez apprendre à intégrer MaxxPoint à Azure Active Directory (Azure AD).

L’intégration de MaxxPoint à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à MaxxPoint.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à MaxxPoint (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le Portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à MaxxPoint, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement MaxxPoint pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MaxxPoint à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-maxxpoint-from-the-gallery"></a>Ajout de MaxxPoint à partir de la galerie
Pour configurer l’intégration de MaxxPoint à Azure AD, vous devez ajouter MaxxPoint à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter MaxxPoint à partir de la galerie, procédez comme suit :**

1. Dans le **[Portail de gestion Azure](https://portal.azure.com)**, dans le panneau de navigation gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **MaxxPoint**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_001.png)

5. Dans le volet des résultats, sélectionnez **MaxxPoint**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec MaxxPoint avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur MaxxPoint équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur MaxxPoint associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans MaxxPoint.

Pour configurer et tester l’authentification unique Azure AD avec MaxxPoint, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test MaxxPoint](#creating-a-maxxpoint-test-user)** pour avoir un équivalent de Britta Simon dans MaxxPoint lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application MaxxPoint.

**Pour configurer l’authentification unique Azure AD avec MaxxPoint, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **MaxxPoint**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_300.png)

3. Dans la section **MaxxPoint Domain and URLs** (Domaines et URL MaxxPoint), si vous souhaitez configurer l’application en **IDP initiated mode** (Mode initié par IDP), vous n’avez aucune opération à effectuer.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_02.png)
    
4. Dans la section **MaxxPoint Domain and URLs** (Domaines et URL MaxxPoint), si vous souhaitez configurer l’application en **SP initiated mode** (Mode initié par SP), procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_03.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://maxxpoint.westipc.com/default/sso/login/entity/<customer-id>-azure`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez [l’équipe de support technique MaxxPoint](mailto:iumsupport@intercall.com).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_400.png)     

6. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_500.png)

7. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_05.png)

8. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_600.png)

9. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_06.png) 

10. Pour obtenir la configuration de l’authentification unique pour votre application, contactez [l’équipe de support MaxxPoint](mailto:iumsupport@intercall.com) en lui fournissant les **métadonnées** téléchargées. 


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-a-maxxpoint-test-user"></a>Création d’un utilisateur de test MaxxPoint

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans MaxxPoint. Collaborez avec [l’équipe de support MaxxPoint](mailto:iumsupport@intercall.com) pour ajouter des utilisateurs dans la plateforme MaxxPoint.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MaxxPoint.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à MaxxPoint, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **MaxxPoint**.

    ![Configurer l’authentification unique](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque MaxxPoint dans le volet d’accès, vous devez être connecté automatiquement à votre application MaxxPoint.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_203.png
