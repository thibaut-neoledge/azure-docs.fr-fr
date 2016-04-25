<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Hightail | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Hightail."
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
	ms.date="04/11/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Hightail

L’objectif de ce didacticiel est de vous montrer comment intégrer Hightail à Azure AD (Azure Active Directory).

L’intégration de Hightail à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Hightail
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Hightail (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise

Pour configurer l’intégration d’Azure AD à Hightail, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Hightail pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Hightail à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Hightail à partir de la galerie
Pour configurer l’intégration de Hightail à Azure AD, vous devez ajouter Hightail, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Hightail à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
 
	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Hightail**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_01.png)

7. Dans le volet de résultats, sélectionnez **Hightail**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Sélection de l’application dans la galerie](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_02.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Hightail avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Hightail équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Hightail associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Hightail.

Pour configurer et tester l’authentification unique Azure AD avec Hightail, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Hightail](#creating-a-hightail-test-user)** pour avoir un équivalent de Britta Simon dans Hightail lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Hightail.

L’application Hightail attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attribut** de l’application. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_51.png)

**Pour configurer l’authentification unique Azure AD avec Hightail, procédez comme suit :**


1. Dans le portail Azure Classic, dans la page d’intégration d’application **Hightail**, dans le menu situé en haut, cliquez sur **Attributs**.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_general_81.png)


1. Dans la boîte de dialogue **Attributs du jeton SAML**, pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :

	| Nom de l'attribut | Valeur de l’attribut |
	| --- | --- |    
	| FirstName | user.givenname |
    | LastName | user.surname |
	| Email | user.mail |
	| UserIdentity | user.mail |

	a. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_general_82.png)


	b. Dans la zone de texte **Nom d’attribut**, tapez le nom d’attribut indiqué pour cette ligne.

    c. Dans la liste **Valeur d’attribut**, sélectionnez la valeur d’attribut indiquée pour cette ligne.

    d. Cliquez sur **Terminé**.
	



1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)



2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Hightail**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_03.png)


3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_04.png)



    a. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : **« https://www.hightail.com/samlLogin?phi\_action=app/samlLogin&subAction=handleSamlResponse »**

	b. Cliquez sur **Suivant**

4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, entrez l’**URL de connexion** et cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_06.png)

	a. Dans la zone de texte URL de connexion, tapez l’URL utilisée par les utilisateurs pour se connecter à votre application Hightail au format suivant : ****https://www.hightail.com/loginSSO**. Il s’agit de la page de connexion commune à tous les clients qui veulent utiliser l’authentification unique.

	b. Cliquez sur **Suivant**

5. Dans la page **Configurer l’authentification unique sur Hightail**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_05.png)


    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat encodé en base 64 sur votre ordinateur.

    b. Cliquez sur **Next**.

	> [AZURE.NOTE] Avant de configurer l’authentification unique sur l’application Hightail, ajoutez votre domaine de messagerie à la liste approuvée de l’équipe Hightail afin que tous les utilisateurs de ce domaine puissent tirer parti de la fonctionnalité d’authentification unique.

6. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Hightail en tant qu’administrateur.

	a. Dans le menu situé en haut, cliquez sur l’onglet **Compte** et sélectionnez **Configure SAML** (Configurer SAML).

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png)


	b. Cochez la case **Enable SAML Authentication** (Activer l’authentification SAML).

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png)

	c. Ouvrez votre certificat encodé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **SAML Token Signing Certificate** (Certificat de signature de jeton SAML).

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png)


	d. Copiez l’URL de connexion distante à partir d’Azure AD vers **SAML Authority (Identity Provider)** (Autorité SAML [fournisseur d’identité]) dans Hightail.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_005.png)
	
	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

	e. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, sélectionnez **Identity Provider (IdP) initiated log in** (Connexion lancée par le fournisseur d’identité). En **mode lancé par le fournisseur de service**, sélectionnez **Service Provider (SP) initiated log in** (Connexion lancée par le fournisseur de service).
	
	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

	f. Copiez l’URL de consommateur SAML pour votre instance et collez-la dans la zone de texte **URL de réponse** comme indiqué à l’étape 4.

	g. Cliquez sur **Enregistrer**.



6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png)


4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_05.png)

    a. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_07.png)


8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Hightail

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Hightail.

Vous n’avez aucune opération à effectuer dans cette section. Hightail prend en charge l’approvisionnement juste-à-temps d’utilisateurs basé sur les revendications personnalisées. Si vous avez configuré les revendications personnalisées comme indiqué dans la section **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** ci-dessus, un utilisateur est automatiquement créé dans l’application s’il n’existe pas encore.

> [AZURE.NOTE] Si vous avez besoin de créer un utilisateur manuellement, vous devez contacter l’équipe de support Hightail via [support@hightail.com](mailto:support@hightail.com).


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Hightail.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Hightail, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.
 
	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Hightail**.

	![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_50.png)


1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Hightail dans le volet d’accès, vous devez être connecté automatiquement à votre application Hightail.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0413_2016-->