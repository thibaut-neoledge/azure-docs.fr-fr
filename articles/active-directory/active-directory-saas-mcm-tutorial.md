---
title: 'Didacticiel : Intégration d’Azure Active Directory à MCM | Microsoft Docs'
description: Découvrez comment utiliser MCM avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/30/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory à MCM
L’objectif de ce didacticiel est de vous montrer comment intégrer MCM à Azure Active Directory (Azure AD).

L’intégration de MCM à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à MCM.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à MCM (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis
Pour configurer l’intégration d’Azure AD à MCM, vous avez besoin des éléments suivants :

* Un abonnement Azure valide
* Un abonnement MCM pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MCM depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## Ajout de MCM depuis la galerie
Pour configurer l’intégration de MCM à Azure AD, vous devez ajouter MCM depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter MCM à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **MCM**.
   
   ![Galerie d’applications](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **MCM**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

## Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec MCM avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur MCM équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur MCM associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans MCM.

Pour configurer et tester l’authentification unique Azure AD avec MCM, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test MCM](#creating-a-mcm-test-user)** pour avoir un équivalent de Britta Simon dans MCM lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application MCM.

**Pour configurer l’authentification unique Azure AD avec MCM, procédez comme suit :**

1. Dans la page d’intégration d’applications **MCM** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à MCM ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Authentification unique Microsoft Azure AD](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Authentification unique avec Microsoft Azure AD")
3. Sur la page Configurer les paramètres d’application, procédez comme suit :
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurer l’URL de l’application")
   
   a. Dans la zone de texte **URL de connexion**, tapez `https://myaba.co.uk/client-access/<company name>/saml.php`.
   
   b. Cliquez sur **Next**
4. Dans la page **Configurer l’authentification unique sur MCM**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurer l’authentification unique")
5. Pour que l’authentification unique soit configurée pour votre application, contactez votre équipe du support technique MCM. Joignez le fichier de métadonnées téléchargé et partagez-le avec l’équipe MCM pour qu’elle configure l’authentification unique de son côté.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurer l’authentification unique")
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurer l’authentification unique")

### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page **Profil utilisateur**, procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)
   
   a. Dans la zone de texte **Prénom**, entrez **Britta**.
   
   b. Dans la zone de texte **Nom**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.

### Création d’un utilisateur de test MCM
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans MCM. Collaborez avec l’équipe du support technique MCM pour ajouter les utilisateurs à la plateforme MCM.

> [!NOTE]
> Vous pouvez utiliser tout autre outil de création de compte d’utilisateur MCM ou des API fournies par MCM pour approvisionner des comptes d’utilisateurs Azure Active Directory.
> 
> 

### Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à MCM.

![Affecter des utilisateurs](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Affecter des utilisateurs")

**Pour affecter Britta Simon à MCM, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Affecter des utilisateurs")
2. Dans la liste des applications, sélectionnez **MCM**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Affecter des utilisateurs")
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Affecter des utilisateurs")

### Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque MCM dans le volet d’accès, vous devez être connecté automatiquement à votre application MCM.

## Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0907_2016-->