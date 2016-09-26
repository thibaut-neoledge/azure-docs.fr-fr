<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Marketo | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Marketo."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Marketo

Dans ce didacticiel, vous allez apprendre à intégrer Marketo à Azure Active Directory (Azure AD).

L’intégration de Marketo à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès Marketo.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Marketo (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à Marketo, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Marketo pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Marketo depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Marketo depuis la galerie
Pour configurer l’intégration de Marketo à Azure AD, vous devez ajouter Marketo, depuis la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Marketo depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Marketo**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)

7. Dans le volet des résultats, sélectionnez **Marketo**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Marketo avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Marketo équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Marketo associé.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Marketo.

Pour configurer et tester l’authentification unique Azure AD avec Marketo, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Marketo](#creating-a-predictix-price-reporting-test-user)** pour avoir un équivalent de Britta Simon dans Marketo lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Marketo.


**Pour configurer l’authentification unique Azure AD avec Marketo, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration d’applications **Marketo**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Sur la page **How would you like users to sign on to Marketo** (Comment voulez-vous que les utilisateurs se connectent à Marketo), sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png)

    a. Dans la zone de texte **Identificateur**, tapez l’URL au format suivant : `https://saml.marketo.com/sp`
	
	b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://login.marketo.com/saml/assertion/<munchkinid>`

	c. Cliquez sur **Suivant**
 
4. Sur la page **Configure single sign-on at Marketo** (Configurer l’authentification unique sur Marketo), procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour obtenir l’ID Munchkin de votre application, connectez-vous à Marketo à l’aide des informations d’identification d’administrateur et procédez comme suit :

	a. Connectez-vous à l’application Marketo à l’aide des informations d’identification d’administrateur.

	b. Cliquez sur le bouton Admin dans le volet de navigation supérieure.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

	c. Accédez au menu Intégration et cliquez sur le lien Munchkin.
	
	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)

	d. Copiez l’ID Munchkin indiqué sur l’écran et complétez votre URL de réponse dans l’Assistant de configuration Azure AD.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)

6.	Pour configurer l’authentification unique de l’application, suivez les étapes ci-dessous :

	a. Connectez-vous à l’application Marketo à l’aide des informations d’identification d’administrateur.

	b. Cliquez sur le bouton Admin dans le volet de navigation supérieure.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

	c. Accédez au menu Intégration et cliquez sur Authentification unique.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png)

	d. Pour activer les paramètres SAML, cliquez sur le bouton Modifier.
	
	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png)

	e. **Activez** les paramètres d’authentification unique.

	f. Entrez l’ID de l’émetteur que vous avez copié à partir de l’Assistant de configuration Azure AD.

	g. Dans la zone de texte d’ID d’entité, tapez l’URL **http://saml.marketo.com/sp**.

	h. Sélectionnez l’emplacement d’ID utilisateur en tant **qu’élément Identificateur de nom**.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)

	> [AZURE.NOTE] Si votre identificateur d’utilisateur n’est pas une valeur UPN, alors changez la valeur dans l’onglet Attribut.
	 
	i. Chargez le certificat que vous avez téléchargé à partir de l’Assistant de configuration Azure AD. Enregistrez les paramètres.

	j. Modifiez les paramètres des pages de redirection.

	k. Copiez l’URL de connexion à partir de l’Assistant de configuration Azure AD dans la zone de texte **URL de connexion**.

	l. Copiez l’URL de déconnexion à partir de l’Assistant de configuration Azure AD dans la zone de texte **Logout URL** (URL de déconnexion).

	m. Dans l’URL d’erreur, copiez l’URL de votre instance Marketo, puis cliquez sur le bouton Enregistrer pour enregistrer les paramètres.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. Pour activer l’authentification unique pour les utilisateurs, procédez comme suit :

	a. Connectez-vous à l’application Marketo à l’aide des informations d’identification d’administrateur.

	b. Cliquez sur le bouton **Admin** dans le volet de navigation supérieure.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

	c. Accédez au menu **Sécurité** et cliquez sur **Login Settings** (Paramètres de connexion).

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)

	d. Vérifiez l’option **Require SSO** (Exiger l’authentification unique) et enregistrez les paramètres.
	
	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

8. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
	
	![Authentification unique Azure AD][10]

8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
	![Authentification unique Azure AD][11]


### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png)

5. Sur la page **Parlez-nous de cet utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Marketo

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Marketo. Suivez ces étapes pour créer un utilisateur dans la plate-forme Marketo.

1. Connectez-vous à l’application Marketo à l’aide des informations d’identification d’administrateur.

2. Cliquez sur le bouton **Admin** dans le volet de navigation supérieure.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

3. Accédez au menu **Sécurité** et cliquez sur **Utilisateurs et rôles**.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)

4. Cliquez sur le lien **Invitation d’un nouvel utilisateur** dans l’onglet Utilisateurs.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png)

5. Dans l’Assistant d’invitation d’un nouvel utilisateur, renseignez les informations suivantes.

	a. Entrez l’adresse **e-mail** de l’utilisateur dans la zone de texte.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)

	b. Entrez le **prénom** dans la zone de texte.
	
	c. Entrez le **nom** dans la zone de texte.

	d. Cliquez sur Suivant.

6. Dans l’onglet **Autorisations**, sélectionnez les rôles d’utilisateur et cliquez sur Suivant.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)

7. Cliquez sur le bouton Envoyer pour envoyer l’invitation d’utilisateur.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. L’utilisateur recevra une notification par e-mail et devra cliquer sur le lien et modifier le mot de passe pour activer le compte.


### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Marketo.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Marketo, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Marketo**.

	![Configurer l’authentification unique](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]


### Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Marketo dans le volet d’accès, vous êtes connecté automatiquement à votre application Marketo.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->