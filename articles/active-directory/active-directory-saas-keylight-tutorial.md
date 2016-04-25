<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Keylight | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Keylight."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Keylight

Dans ce didacticiel, vous allez apprendre à intégrer Keylight à Azure AD (Azure Active Directory).

L’intégration de Keylight à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Keylight
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Keylight (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise

Pour configurer l’intégration d’Azure AD à Keylight, vous avez besoin des éléments suivants :

- Un abonnement Azure
- Un abonnement Keylight pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Keylight à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Keylight à partir de la galerie
Pour configurer l’intégration de Keylight à Azure AD, vous devez ajouter Keylight, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Keylight à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. 

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Keylight**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. Dans le volet de résultats, sélectionnez **Keylight**, puis cliquez sur **Terminer** pour ajouter l’application.



##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Keylight avec un utilisateur de test appelé « Britta Simon ».

Pour configurer et tester l’authentification unique Azure AD avec Keylight, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Keylight](#creating-a-Keylight-test-user)** pour avoir un équivalent de Britta Simon dans Keylight lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Keylight.


**Pour configurer l’authentification unique Azure AD avec Keylight, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration d’application **Keylight**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]


2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Keylight**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :
 
	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png)


    a. Dans la zone de texte URL de connexion, tapez l’URL utilisée par les utilisateurs pour se connecter à votre application Keylight au format suivant : **« https://<nom\_entreprise>.keylightgrc.com/Login.aspx?saml=1 »**.


4. Dans la page **Configurer l’authentification unique sur Keylight**, procédez comme suit :
 
	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour activer l’authentification unique dans Keylight, procédez comme suit :
 
    a. Connectez-vous à votre compte Keylight en tant qu’administrateur.

    b. Dans le menu situé en haut, cliquez sur **Person** (Personne), puis sélectionnez **Keylight Setup** (Installation de Keylight).
       
	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/401.png)

    c. Dans l’arborescence sur la gauche, cliquez sur **SAML**.

	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/402.png)

    d. Dans la boîte de dialogue **SAML Settings** (Paramètres SAML), cliquez sur **Edit** (Modifier).

	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/404.png)
  

5. Dans la page de boîte de dialogue **Edit SAML Settings** (Modifier les paramètres SAML), procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/405.png)

    a. Affectez à **SAML authentication** (Authentication SAML) la valeur **Active**.


    b. Dans le portail classique Azure AD, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité).

    c. Dans le portail classique Azure AD, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité).

    d. Cliquez sur **Choose File** (Choisir un fichier) pour sélectionner votre certificat Keylight téléchargé, puis sur **Ouvrir** pour charger le certificat.


    e. Affectez à **SAML User Id location** (Emplacement de l’ID utilisateur SAML) la valeur **NameIdentifier element of the subject statement** (Élément NameIdentifier de l’instruction Subject).
   
    f. Indiquez la valeur **Keylight Service Provider (Fournisseur de service Keylight) au format suivant : **https://&lt;Company Nom&gt;.keylightgrc.com**.

    g. Affectez à **Auto-provision users** (Configuration automatique des utilisateurs) la valeur **Active**.

    h. Affectez à **Auto-provision account type** (Configuration automatique du type de compte) la valeur **Full User** (Utilisateur global).

    i. Pour **Auto-provision security role** (Configuration automatique du rôle de sécurité), sélectionnez **Standard User with SAML** (Utilisateur standard avec SAML).
   
    j. Pour **Auto-provision security config** (Configuration automatique des paramètres de sécurité), sélectionnez **Standard User Configuration** (Configuration utilisateur standard).
   
    k. Dans la zone de texte Email attribute (Attribut de messagerie), tapez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. Dans la zone de texte **First name attribute** (Attribut de prénom), tapez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. Dans la zone de texte **Last name attribute** (Attribut de nom), tapez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Cliquez sur **Enregistrer**.
   
  
   
  
6. Dans le portail Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Azure.

Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]



**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png)


2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png)


4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png)

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Keylight

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Keylight. Keylight prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors de l’accès à Keylight si l’utilisateur n’existe pas encore. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe de support Keylight.


### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Keylight.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Keylight, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Keylight**.

	![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Keylight dans le volet d’accès, vous devez être connecté automatiquement à votre application Keylight.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0413_2016-->