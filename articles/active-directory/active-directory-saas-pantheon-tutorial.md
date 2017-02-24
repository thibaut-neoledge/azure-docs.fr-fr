---
title: "Didacticiel : Intégration d’Azure Active Directory à Pantheon | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pantheon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 52a42d6705d0d2cfb86865027f8315411d7aece0
ms.openlocfilehash: b14872daab1bddf18d68b1f0fb23fc74c12078ed


---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Didacticiel : Intégration d’Azure Active Directory à Pantheon

Dans ce didacticiel, vous allez apprendre à intégrer Pantheon à Azure Active Directory (Azure AD).

L’intégration de Pantheon dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Pantheon
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Pantheon (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Pantheon, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Pantheon pour lequel l’authentification unique est activée


>[!NOTE] 
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Pantheon à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-pantheon-from-the-gallery"></a>Ajout de Pantheon à partir de la galerie
Pour configurer l’intégration de Pantheon avec Azure AD, vous devez ajouter Pantheon à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Pantheon à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans le champ de recherche, tapez **Pantheon**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_01.png)

7. Dans le volet de résultats, sélectionnez **Pantheon**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Pantheon avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Pantheon équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Pantheon associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Pantheon.

Pour configurer et tester l’authentification unique Azure AD avec Pantheon, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Pantheon](#creating-a-pantheon-test-user)** pour avoir un équivalent de Britta Simon dans Pantheon lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Pantheon.


**Pour configurer l’authentification unique Azure AD avec Pantheon, procédez comme suit :**

L’application Pantheon attend l’assertion SAML dans un format spécifique. Vous devez donc définir la valeur de l’attribut NameIdentifier sur l’adresse électronique de l’utilisateur. Par défaut, Azure AD utilise UserPrincipalName pour l’attribut NameIdentifier. Mais pour une intégration réussie, vous devez ajuster cette valeur pour la faire correspondre à l’adresse électronique de l’utilisateur.
L’intégration ne fonctionnera qu’une fois le mappage correct effectué.

![Configurer l’authentification unique](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_06.png)
    
1. Dans le portail classique, dans la page d’intégration d’applications **Pantheon**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
     
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Pantheon ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_03.png) 

3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_04.png) 

    a. Dans la zone de texte **Identificateur** , tapez l’URN au format suivant : `urn:auth0:pantheon:<orgname>-SSO`
    
    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO` 

    c. Cliquez sur **Suivant**
 
4. Sur la page **Configurer l’authentification unique sur Pantheon**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Suivant**.


5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de [support Pantheon](https://pantheon.io/docs/getting-support) et fournissez-lui les informations suivantes :

    • Le **certificat**

    • **L’URL du service d’authentification unique**

    >[!NOTE] 
    >Vous devez également fournir les informations des domaines de messagerie et des dates/heures auxquelles vous souhaitez activer cette connexion. Vous trouverez plus de détails à ce propos [ici](https://pantheon.io/docs/sso-organizations/)

6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
 
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-an-pantheon-test-user"></a>Création d’un utilisateur de test Pantheon

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Pantheon. Suivez les étapes ci-dessous pour ajouter l’utilisateur dans Pantheon. 

>[!NOTE] 
>Pour que l’authentification unique fonctionne, vous devez d’abord créer un utilisateur dans Pantheon.

1. Connectez-vous à Pantheon avec les informations d’identification de l’administrateur.

2. Accédez à la page de tableau de bord **Organisation**.
 
3. Cliquez sur **People**.

4. Cliquez sur **Add User**.

5. Saisissez l’adresse de messagerie de l’utilisateur.

6. Choisissez le rôle de l’utilisateur.

7. Cliquez sur **Add User**.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pantheon.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Pantheon, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Pantheon**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Pantheon dans le volet d’accès, vous devez être connecté automatiquement à votre application Pantheon.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


