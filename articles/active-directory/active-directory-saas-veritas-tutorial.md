---
title: "Didacticiel : intégration d’Azure Active Directory à Veritas Enterprise Vault.cloud SSO | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Veritas Enterprise Vault.cloud SSO."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a8f9ae76cd914224b3715857b6fd37750f13cd7b
ms.openlocfilehash: 2d6dccdcb7eb6c08d707c2182f9af8fe4824e6f5


---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Didacticiel : intégration d’Azure Active Directory à Veritas Enterprise Vault.cloud SSO

Dans ce didacticiel, vous allez apprendre à intégrer Veritas Enterprise Vault.cloud SSO à Azure Active Directory (Azure AD).

L’intégration du logiciel Veritas Enterprise Vault.cloud SSO à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Veritas Enterprise Vault.cloud SSO
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Veritas Enterprise Vault.cloud SSO (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Veritas Enterprise Vault.cloud SSO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Veritas Enterprise Vault.cloud SSO avec l’authentification unique activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Veritas Enterprise SSO Vault.cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Ajout de Veritas Enterprise SSO Vault.cloud à partir de la galerie
Pour configurer son intégration à Azure AD, vous devez ajouter Veritas Enterprise Vault.cloud SSO à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Veritas Enterprise Vault.cloud SSO à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **Veritas Enterprise Vault.cloud SSO**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_01.png)
7. Dans le volet des résultats, sélectionnez **Veritas Enterprise Vault.cloud SSO**, puis cliquez sur **Terminer** pour ajouter l’application.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Veritas Enterprise Vault.cloud SSO, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Veritas Enterprise Vault.cloud SSO équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et un utilisateur Veritas Enterprise Vault.cloud SSO associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Veritas Enterprise Vault.cloud SSO.

Pour configurer et tester l’authentification unique Azure AD avec Veritas Enterprise Vault.cloud SSO, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Veritas Enterprise Vault.cloud SSO](#creating-a-Veritas Enterprise Vault.cloud SSO-test-user)** pour avoir un équivalent de Britta Simon dans Veritas Enterprise Vault.cloud SSO, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Veritas Enterprise Vault.cloud SSO.


**Pour configurer l’authentification unique Azure AD avec Veritas Enterprise Vault.cloud SSO, procédez comme suit :**

1. Dans le portail Classic, sur la page d’intégration d’applications **Veritas Enterprise Vault.cloud SSO**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
     
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Veritas Enterprise Vault.cloud SSO ?**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_03.png) 

3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_04.png) 

    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par les utilisateurs pour se connecter à votre application Veritas Enterprise Vault.cloud SSO au format suivant : `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`
    
     
4. Sur la page **Configurer l’authentification unique sur Veritas Enterprise Vault.cloud SSO**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Suivant**.


5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support de Veritas Enterprise Vault.cloud SSO sur le <a href="https://www.veritas.com/content/support/en_US/62696.html ">site web d’assistance</a>, et envoyez-lui les éléments suivants :

    • Les **métadonnées**

    • **L’URL d’authentification unique SAML**

6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
 
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Création d’un utilisateur de test Veritas Enterprise Vault.cloud SSO

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Veritas Enterprise Vault.cloud SSO. Travaillez avec l’équipe de support technique de Veritas Enterprise Vault.cloud SSO sur le <a href="https://www.veritas.com/content/support/en_US/62696.html ">site web de support</a> pour ajouter les utilisateurs dans la plateforme Veritas Enterprise Vault.cloud SSO.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Veritas Enterprise Vault.cloud SSO.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Veritas Enterprise Vault.cloud SSO, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Veritas Enterprise Vault.cloud SSO**.

    ![Configurer l’authentification unique](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Veritas Enterprise Vault.cloud SSO dans le volet d’accès, vous devez être connecté automatiquement à votre application Veritas Enterprise Vault.cloud SSO.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_205.png




<!--HONumber=Nov16_HO5-->


