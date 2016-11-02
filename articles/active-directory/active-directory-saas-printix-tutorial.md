<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Printix | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Printix."
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
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Printix

Dans ce didacticiel, vous allez apprendre à intégrer Printix à Azure Active Directory (Azure AD).

L’intégration de Printix à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Printix.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Printix (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à Printix, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Printix pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Printix à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Printix à partir de la galerie
Pour configurer l’intégration de Printix à Azure AD, vous devez ajouter Printix à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Printix à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Printix**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/tutorial_printix_01.png)
7. Dans le volet de résultats, sélectionnez **Printix**, puis cliquez sur **Terminer** pour ajouter l’application.



##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Printix avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Printix équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Printix associé doit être établie.

Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username (Nom d’utilisateur)** dans Printix.

Pour configurer et tester l’authentification unique Azure AD avec Printix, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Printix](#creating-a-printix-test-user)** pour avoir un équivalent de Britta Simon dans Printix lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Printix.


**Pour configurer l’authentification unique Azure AD avec Printix, procédez comme suit :**

1. Dans le portail classique, dans la page d’intégration d’applications **Printix**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Printix**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_03.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_04.png)

    a. Dans la zone de texte **URL de réponse**, tapez `https://auth.printix.net/saml/SSO`.
	
	b. Cliquez sur **Suivant**.
 
4. Dans la page **Configurer l’authentification unique sur Printix**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_05.png)

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Suivant**.


5. Connectez-vous à votre client Printix en tant qu’administrateur.


6. Dans le menu situé en haut, cliquez sur l’icône dans le coin supérieur droit et sélectionnez **Authentification** (Authentification).

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_06.png)

7. Dans l’onglet **Setup** (Configuration), sélectionnez **Enable Azure/Office 365 authentication** (Activer l’authentification Azure/Office 365).

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_07.png)

8. Dans l’onglet **Azure**, entrez l’URL des métadonnées de fédération dans la zone de texte **Federation metadata document** (Document de métadonnées de fédération).
	
	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_08.png)

	a. Joignez le fichier XML de métadonnées que vous avez téléchargé à l’étape 4 pour l’adresser à l’équipe de support Printix via **support@printix.net**. Elle charge alors le fichier XML et vous fournit une URL des métadonnées de fédération.


9. Cliquez sur le bouton **Test**, puis sur le bouton **OK** si le test est réussi.

	a. La page Azure Active Directory s’affiche après que vous avez cliqué sur le bouton **Test**. Le message The test was successful (Test réussi) signifie qu’une fois que vous avez entré les informations d’identification de votre compte test Azure, le message Settings tested OK (Paramètres testés correctement) s’affiche. Cliquez alors sur le bouton **OK**.

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_09.png)


10. Cliquez sur le bouton **Save** (Enregistrer) dans la page **Authentication** (Authentification).


11. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
	
	![Authentification unique Azure AD][10]

12. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
	![Authentification unique Azure AD][11]


### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Printix

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Printix. Printix prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Printix s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe de support Printix.


### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Printix.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Printix, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Printix**.

	![Configurer l’authentification unique](./media/active-directory-saas-printix-tutorial/tutorial_printix_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]


### Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Printix dans le volet d’accès, vous devez être connecté automatiquement à votre application Printix.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-printix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-printix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-printix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-printix-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-printix-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-printix-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-printix-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-printix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-printix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-printix-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-printix-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-printix-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-printix-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->