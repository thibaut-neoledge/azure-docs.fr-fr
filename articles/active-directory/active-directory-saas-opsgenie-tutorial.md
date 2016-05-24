<properties
	pageTitle="Didacticiel : Intégration d’Azure AD à OpsGenie | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et OpsGenie."
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
	ms.date="04/26/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à OpsGenie

L’objectif de ce didacticiel est de vous montrer comment intégrer OpsGenie dans Azure AD (Azure Active Directory).

L’intégration d’OpsGenie dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à OpsGenie.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à OpsGenie (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à OpsGenie, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement OpsGenie pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de OpsGenie à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de OpsGenie à partir de la galerie
Pour configurer l’intégration de OpsGenie à Azure AD, vous devez ajouter OpsGenie à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter OpsGenie à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **OpsGenie**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. Dans le volet des résultats, sélectionnez **OpsGenie**, puis cliquez sur **Terminé** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec OpsGenie au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur OpsGenie équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur OpsGenie associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans OpsGenie.

Pour configurer et tester l’authentification unique Azure AD avec OpsGenie, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test OpsGenie](#creating-a-opsgenie-test-user)** pour avoir un équivalent de Britta Simon dans OpsGenie lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application OpsGenie.



**Pour configurer l’authentification unique Azure AD avec OpsGenie, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration d’application **OpsGenie**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à OpsGenie**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png)


    a. Dans la zone de texte URL d’authentification, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application OpsGenie, au format suivant : **« https://app.opsgenie.com/auth/login »**.

    > [AZURE.NOTE] Contactez votre [équipe de support OpsGenie](mailto:support@opsgenie.com) si vous avez de besoin de votre URL d’authentification.

    b. Cliquez sur **Next**.


4. Sur la page **Configurer l’authentification unique sur OpsGenie**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. Nous aurons besoin de ce certificat et des URL de métadonnées (ID d’entité, URL d’authentification unique et URL de déconnexion unique) pour configurer l’authentification unique sur OpsGenie.

    b. Cliquez sur **Next**.


5. Ouvrez une autre instance de navigateur, puis connectez-vous à OpsGenie en tant qu’administrateur.

6. Cliquez sur **Paramètres**, puis cliquez sur l’onglet **Authentification unique**.
 
	![Authentification unique OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png)

7. Pour activer l’authentification unique, sélectionnez **Activé**.

	![Paramètres OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png)
   
8. Dans la section **Fournisseur** cliquez sur l’onglet **Azure Active Directory**.

	![Paramètres OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png)
    
9. Sur la page de boîte de dialogue Azure Active Directory, procédez comme suit :
 
	![Paramètres OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)

    a. Sur la page **Configurer l’authentification unique sur OpsGenie** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Point de terminaison SAML 2.0**.

    b. Créez un fichier codé en base 64 à partir du certificat téléchargé.
    
    > [AZURE.NOTE] Pour plus d’informations, consultez [How to convert a binary certificate into a text file](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat X.500**.

    d. Cliquez sur **Enregistrer les modifications**.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.



![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test OpsGenie

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans OpsGenie.

1.	Dans une fenêtre de navigateur web, connectez-vous à votre client OpsGenie en tant qu’administrateur.

2.	Accédez à la liste Utilisateurs en cliquant sur **Utilisateur** dans le volet gauche.
   
	![Paramètres OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png)

3.	Cliquez sur **Ajouter un utilisateur**.

3.	Dans la boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :

	![Paramètres OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png)

    a. Dans la zone de texte **E-mail**, tapez l’adresse de messagerie de Simon Britta dans Azure Active Directory.

    b. Dans la zone de texte **Nom complet**, tapez **Britta Simon**.

    c. Cliquez sur **Enregistrer**.

Brian obtiendra un courrier électronique d’instructions pour configurer son profil.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à OpsGenie.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à OpsGenie, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
 
	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **OpsGenie**.

	![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette OpsGenie dans le volet d’accès, vous devez être connecté automatiquement à votre application OpsGenie.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0511_2016-->