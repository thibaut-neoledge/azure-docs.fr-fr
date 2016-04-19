<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Heroku | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Heroku."
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
	ms.date="04/05/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Heroku

Dans ce didacticiel, vous allez apprendre à intégrer Heroku à Azure Active Directory (Azure AD).

L’intégration de Heroku à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Heroku.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Heroku (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à Heroku, vous avez besoin des éléments suivants :

- Un abonnement Azure
- Un abonnement Heroku pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Heroku à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Heroku à partir de la galerie
Pour configurer l’intégration de Heroku à Azure AD, vous devez ajouter Heroku à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Heroku à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **Heroku**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_01.png)<br>
7. Dans le volet de résultats, sélectionnez **Heroku**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>

##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Heroku avec un utilisateur de test appelé "Britta Simon".

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Heroku équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Heroku associé doit être établie.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Heroku.

Pour configurer et tester l’authentification unique Azure AD avec Heroku, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Heroku](#creating-an-heroku-test-user)** pour avoir un équivalent de Britta Simon dans Heroku lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Classic et configurer l’authentification unique dans votre application Heroku.


**Pour configurer l’authentification unique Azure AD avec Heroku, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Heroku**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Heroku**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_03.png) <br>

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_04.png) <br>


    a. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Heroku au format suivant : **"https://sso.heroku.com/saml/nom\_entreprise>/init"**.

    b. Cliquez sur **Next**.


4. Dans la page **Configurer l’authentification unique sur Heroku**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_05.png) <br>

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour activer SSO dans Heroku, procédez comme suit :
 
    a. Connectez-vous au compte Heroku en tant qu'administrateur.

    b. Cliquez sur l'onglet **Paramètres**.

    c. Sur la page **Authentification unique**, cliquez sur **Charger les métadonnées**.
 
    d. Chargez le fichier de métadonnées que vous avez téléchargé à partir du portail Azure Classic.

    e. Lorsque l'installation est réussie, les administrateurs verront une boîte de dialogue de confirmation, et l'URL de l'authentification unique pour les utilisateurs finaux apparaît.

    f. Copiez votre **URL de connexion Heroku**, puis, dans le portail Azure AD Classic, revenez à la page **Configurer les paramètres de l'application** et collez la valeur dans la zone de texte **URL d'authentification**.
  
    <br>![Configurer l’authentification unique](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) <br><br>

    g. Cliquez sur **Next**.
  
6. Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. Dans la page **Confirmation de l'authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_09.png) <br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) <br>

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) <br>

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_06.png) <br>

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_07.png) <br>

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_08.png) <br>

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Heroku

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Heroku. Heroku prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un nouvel utilisateur est créé lors de l’accès Heroku si l’utilisateur n’existe pas encore. Une fois le compte approvisionné, l'utilisateur final reçoit un message de vérification et doit cliquer sur le lien d'accusé de réception.

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l'équipe de support Heroku.


### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Heroku. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à Heroku, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **Heroku**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la mosaïque Heroku dans le volet d’accès, vous devez être connecté automatiquement à votre application Heroku.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0406_2016-->