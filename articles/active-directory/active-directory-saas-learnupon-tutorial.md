<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Novatus | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et LearnUpon."
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
	ms.date="02/11/2016"
	ms.author="jeedes"/>


# Didacticiel : intégration d’Azure Active Directory à LearnUpon

L’objectif de ce didacticiel est de vous montrer comment intégrer LearnUpon à Azure Active Directory (Azure AD).<br>L’intégration de LearnUpon à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LearnUpon.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à LearnUpon (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic. 
- 

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à LearnUpon, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LearnUpon pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LearnUpon à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de LearnUpon à partir de la galerie
Pour configurer l’intégration de LearnUpon à Azure AD, vous devez ajouter LearnUpon de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter LearnUpon à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, saisissez **LearnUpon**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)<br>
7. Dans le volet des résultats, sélectionnez **LearnUpon**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)<br>
##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique avec Azure AD avec LearnUpon en fonction d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur de LearnUpon équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur de LearnUpon associé doit être établie.<br> Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username (Nom d’utilisateur)** dans LearnUpon.

Pour configurer et tester l’authentification unique avec Azure AD avec LearnUpon, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test LearnUpon](#creating-a-learnupon-test-user)** pour avoir un équivalent de Britta Simon dans LearnUpon lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application LearnUpon.



**Pour configurer l’authentification unique Azure AD avec LearnUpon, procédez comme suit :**

1. Dans le portail Azure AD, puis dans la page d’intégration d’application **LearnUpon**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à LearnUpon**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) <br>

3. Dans la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) <br>


    a. Dans la zone de texte URL de réponse, tapez l’URL Assertion Consumer Service à l’aide du modèle suivant : **« https://<nom\_société>.learnupon.com/saml/consumer »**.


4. Dans la page **Configurer l’authentification unique sur LearnUpon**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) <br>

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. Nous aurons besoin de ce certificat et des URL de métadonnées (ID d’entité, URL de connexion d’authentification unique et URL de déconnexion unique) pour configurer l’authentification unique sur LearnUpon.

    b. Cliquez sur **Next**.


5. Ouvrez une autre connexion d’instance de navigateur dans l’instance LearnUpon avec l’utilisateur administrateur pour définir l’**authentification unique SAML** sur LearnUpon. Une fois connecté à LearnUpon, un écran semblable à celle-ci apparaît. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) <br>

	a. Cliquez sur l’onglet **settings** pour ouvrir la fenêtre des paramètres.<br> b. Cliquez sur **Single Sign On - SAML**<br> c. Cliquez sur **General Settings** pour configurer les paramètres SAML. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) <br> d. Remplissez le formulaire **General Settings** comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) <br> d1. Cochez la case **Enabled** pour activer SAML sur ce portail.<br> d2. Choisissez **version 2.0**.<br> d3. Choisissez **No Skip conditions**.<br> d4. **SAML Token POST param name** est le nom de paramètre de publication de demande correspondant à l’URL de consommateur SAML indiquée ci-dessus, qui contient l’assertion SAML à vérifier et à authentifier. Par exemple, **SAMLResponse**.<br> d5. **Name Identifier Format** indique à quel emplacement dans votre assertion SAML réside l’identificateur de l’utilisateur (adresse e-mail), par exemple, **urn:oasis:names:tc:SAML:1.1:nameid- format:emailAddress**.<br> d6. **Identify Provider Location** indique l’emplacement vers lequel l’utilisateur est redirigé s’il clique sur votre icône chargée à partir de l’écran de connexion de votre portail.<br> d7. Copiez l’**URL du service de déconnexion unique** depuis l’écran de configuration Azure vers **Sign out URL**. <br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_09.png) <br> d8. Cliquez sur le lien **Manage finger prints** sous Certificate Finger Prints pour charger l’empreinte du certificat. <br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_10.png) <br> d9. Cliquez sur le bouton Save. e. Cliquez sur **User Settings** pour configurer les paramètres utilisateur SAML. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) <br> e1. **First Name Identifier Format** indique à quel emplacement dans votre assertion SAML réside le prénom des utilisateurs, par exemple, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**. e2. **Last Name Identifier Format** indique à quel emplacement dans votre assertion SAML réside le nom des utilisateurs, par exemple, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ surname**.


6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br>![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) <br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) <br>

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) <br>

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) <br>

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) <br>

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) <br>

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test LearnUpon

L’objectif de cette section est de créer l’utilisateur Britta Simon dans LearnUpon. LearnUpon prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé au moment d’une tentative d’accès à LearnUpon s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique LearnUpon.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à LearnUpon. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à LearnUpon, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **LearnUpon**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Quand vous cliquez sur la vignette LearnUpon dans le volet d’accès, vous devez être connecté automatiquement à votre application LearnUpon.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0218_2016-->