---
title: 'Didacticiel : Intégration d’Azure Active Directory à Predictix Assortment Planning | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Predictix Assortment Planning.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory à Predictix Assortment Planning
Dans ce didacticiel, vous allez apprendre à intégrer Predictix Assortment Planning à Azure Active Directory (Azure AD).

L’intégration de Predictix Assortment Planning à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Predictix Assortment Planning.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Predictix Assortment Planning (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis
Pour configurer l’intégration d’Azure AD à Predictix Assortment Planning, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Predictix Assortment Planning pour lequel l’authentification unique est activée.

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## Description du scénario
Dans ce didacticiel, vous allez tester l’authentification unique Microsoft Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Predictix Assortment Planning à partir de la galerie
2. Configuration et test de l’authentification unique Microsoft Azure AD

## Ajout de Predictix Assortment Planning à partir de la galerie
Pour configurer l’intégration de Predictix Assortment Planning à Azure AD, vous devez ajouter Predictix Assortment Planning à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Predictix Assortment Planning à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **Predictix Assortment Planning**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)
7. Dans le volet des résultats, sélectionnez **Predictix Assortment Planning**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)

## Configuration et test de l’authentification unique Microsoft Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Microsoft Azure AD avec Predictix Assortment Planning avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Predictix Assortment Planning équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Predictix Assortment Planning associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Predictix Assortment Planning.

Pour configurer et tester l’authentification unique Microsoft Azure AD avec Predictix Assortment Planning, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Microsoft Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Microsoft Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Predictix Assortment Planning](#creating-a-predictix-price-reporting-test-user)** pour avoir un équivalent de Britta Simon dans Predictix Assortment Planning associé à sa représentation dans Azure AD.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Microsoft Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Microsoft Azure AD
Dans cette section, vous allez activer l’authentification unique Microsoft Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Predictix Assortment Planning.

**Pour configurer l’authentification unique Microsoft Azure AD avec Predictix Assortment Planning, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration d’application **Predictix Assortment Planning**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Predictix Assortment Planning**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png)
3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png)
   
    a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application Predictix Assortment Planning, au format suivant : **https://\<nom entreprise-tarification>.ap.predictix.com/sso/request**.
   
    b. Cliquez sur **Next**
4. Sur la page **Configurer l’authentification unique sur Predictix Assortment Planning**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support technique Predictix Assortment Planning et fournissez-lui les éléments suivants :
   
    • Le certificat téléchargé
   
    • **L’ID d’entité**
   
    • **L’URL d’authentification unique SAML**
   
    • **L’URL du service de déconnexion unique**
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png)
5. Sur la page **Parlez-nous de cet utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **Prénom**, entrez **Britta**.
   
   b. Dans la zone de texte **Nom**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.

### Création d’un utilisateur de test Predictix Assortment Planning
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Predictix Assortment Planning. Travaillez avec l’équipe de support technique Predictix Assortment Planning pour ajouter des utilisateurs dans la plateforme Predictix Assortment Planning.

### Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Predictix Assortment Planning.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Predictix Assortment Planning, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Predictix Assortment Planning**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Microsoft Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Predictix Assortment Planning dans le volet d’accès, vous devez être connecté automatiquement à votre application Predictix Assortment Planning.

## Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0824_2016-->