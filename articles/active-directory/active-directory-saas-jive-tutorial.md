---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Jive | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jive.
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
ms.date: 09/01/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory avec Jive
Dans ce didacticiel, vous allez apprendre à intégrer Jive à Azure Active Directory (Azure AD).

L’intégration de Jive dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Jive
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Jive (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis
Pour configurer l’intégration d’Azure AD avec Jive, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Jive pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Jive depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## Ajout de Jive depuis la galerie
Pour configurer l’intégration de Jive avec Azure AD, vous devez ajouter Jive disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Jive à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **Jive**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. Dans le volet des résultats, sélectionnez **Jive**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)

## Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jive avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jive équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Jive associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Jive.

Pour configurer et tester l’authentification unique Azure AD avec Jive, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Jive](#creating-a-jive-test-user)** pour avoir un équivalent de Britta Simon dans Jive lié à la représentation Azure AD associée.
4. **[Configuration de l’approvisionnement en utilisateurs](#configuring-user-provisioning)** : comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur Jive.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Jive.

**Pour configurer l’authentification unique Azure AD avec Jive, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration d’applications **Jive**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Jive**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png)
3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png)
   
    a. Dans la zone de texte **URL d'authentification**, tapez l'URL utilisée par vos utilisateurs pour se connecter à votre application Jive, au format suivant : **https://\<nom client>.jivecustom.com**.
   
    b. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur Jive**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
5. Connectez-vous à votre client Jive en tant qu’administrateur.
6. Dans le menu du haut, cliquez sur « **Saml** ».
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)
   
    a. Sélectionnez **Activé** sous l’onglet **Général**.
   
    b. Cliquez sur le bouton « **Enregistrer tous les paramètres saml** ».
7. Accédez à l’onglet « **Idp Metadata** (Métadonnées du fournisseur d’identité) ».
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a. Copiez le contenu du fichier XML de métadonnées téléchargé et collez-le dans la zone de texte **Métadonnées du fournisseur d'identité (IDP)**.
   
    b. Cliquez sur le bouton « **Enregistrer tous les paramètres saml** ».
8. Accédez à l’onglet « **Mappage d’attributs utilisateur** ».
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. Dans la zone de texte **E-mail**, copiez et collez le nom de la valeur **mail**.
   
    b. Dans la zone de texte **Prénom**, copiez et collez le nom de la valeur **givenname**.
   
    c. Dans la zone de texte **Nom**, copiez et collez le nom de la valeur **surname**.
9. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. ![Authentification unique Azure AD][10]
10. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. ![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png)
5. Sur la page **Parlez-nous de cet utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **Prénom**, entrez **Britta**.
   
   b. Dans la zone de texte **Nom**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.

### Création d’un utilisateur de test Jive
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Jive. Collaborez avec l’équipe du support technique Jive pour ajouter des utilisateurs dans la plate-forme Jive.

### Configuration de l'approvisionnement des utilisateurs
Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur Jive. Dans le cadre de cette procédure, vous devez fournir un jeton de sécurité à demander sur Jive.com.

La capture d’écran suivante présente un exemple de la boîte de dialogue associée dans Azure AD :

![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-jive-tutorial/IC698794.png "Configurer l’approvisionnement des utilisateurs")

#### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Dans le portail de gestion Azure, dans la page d’intégration d’application **Jive**, cliquez sur **Configurer l’approvisionnement des utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement des utilisateurs**.
2. Dans la page **Entrez vos informations d’identification Jive pour activer la configuration automatique d’un utilisateur**, indiquez les paramètres de configuration suivants :
   
   1. Dans la zone de texte **Nom d’utilisateur admin Jive**, tapez le nom d’un compte Jive auquel le profil **System Administrator** est attribué dans Jive.com.
   2. Dans la zone de texte **Mot de passe de l’admin Jive**, tapez le mot de passe de ce compte.
   3. Dans la zone de texte **URL de locataire Jive**, tapez l’URL de locataire Jive.
      
      > [!NOTE]
      > L’URL de locataire Jive est celle utilisée par votre organisation pour se connecter à Jive. En règle générale, l’URL a le format suivant :**www.<organisation>.jive.com**.
      > 
      > 
   4. Cliquez sur **Valider** pour vérifier votre configuration.
   5. Cliquez sur le bouton **Suivant** pour ouvrir la page **Confirmation**.
3. Dans la page **Confirmation**, cliquez sur la coche pour enregistrer votre configuration.

Vous pouvez maintenant créer un compte de test, attendre 10 minutes, puis vérifier la synchronisation du compte à Jive.com.

### Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jive.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Jive, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Jive**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### Test de l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Jive dans le volet d’accès, vous devez être connecté automatiquement à votre application Jive.

## Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->