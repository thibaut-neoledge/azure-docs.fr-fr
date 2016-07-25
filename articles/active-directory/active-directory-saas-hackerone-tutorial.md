<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec HackerOne | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et HackerOne."
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
	ms.date="07/09/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à HackerOne

Dans ce didacticiel, vous intégrerez HackerOne à Azure Active Directory (Azure AD).

L’intégration de HackerOne à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à HackerOne.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à HackerOne (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à HackerOne, vous avez besoin des éléments suivants :

- Un abonnement Azure
- Un abonnement HackerOne pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de HackerOne à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de HackerOne à partir de la galerie
Pour intégrer HackerOne à Azure AD, vous devez ajouter HackerOne, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter HackerOne à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

![Applications][4]

6. Dans la zone de recherche, tapez **HackerOne**.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. Dans le volet de résultats, sélectionnez **HackerOne**, puis cliquez sur **Terminer** pour ajouter l’application.


##  Configuration et test de l’authentification unique Azure AD
Ensuite, vous configurez et testez Azure AD l'authentification unique avec HackerOne basée sur un utilisateur de test appelé "Britta Simon".

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HackerOne équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur HackerOne associé doit être établie. Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans HackerOne.

Pour configurer et tester l’authentification unique Azure AD avec HackerOne, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test HackerOne](#creating-a-hackerone-test-user)** pour avoir un équivalent de Britta Simon dans Certify lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Vous allez maintenant activer l’authentification unique Azure AD dans le portail Classic et configurer l’authentification unique dans votre application HackerOne.

Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

**Pour configurer l’authentification unique Azure AD avec HackerOne, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **HackerOne**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à HackerOne**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png)


    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application HackerOne, au format suivant : **https://hackerone.com/<nomentreprise>/authentication**.

    b. Contactez l’équipe de support HackerOne à l’adresse [support@hackerone.com](mailto:support@hackerone.com) pour obtenir l’URL du locataire.

	c. Dans la zone de texte **Identificateur**, tapez l’URL du locataire.

	d. Cliquez sur **Next**.


4. Dans la boîte de dialogue **Configurer l’authentification unique sur HackerOne**, procédez comme suit, puis cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


1. Connectez-vous à votre client HackerOne en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Paramètres**.

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png)

1. Accédez à **Authentication** et cliquez sur **Add SAML settings**.

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png)


1. Dans la boîte de dialogue **SAML Settings**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png)

    a. Dans la zone de texte **Email Domain**, entrez un domaine enregistré.

	b. Dans le portail Azure Classic, copiez la valeur de l’**URL du service d’authentification unique** et collez-la dans la zone de texte URL d’authentification unique.

    c. Créez un fichier **codé en base 64** à partir du certificat téléchargé.

       >[AZURE.TIP] Pour plus d’informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).
	
    d. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X509 Certificate**.

    e. Cliquez sur **Enregistrer**.


1. Dans la boîte de dialogue Authentication Settings, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png)

    a. Cliquez sur **Run test**.

    b. Si la valeur du champ **Status** est égale à **Last test status: created**, contactez votre équipe de support HackerOne à l’adresse [support@hackerone.com](mailto:support@hackerone.com) pour demander une révision de votre configuration.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD

Vous allez maintenant créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test SECURE DELIVER dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png)

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.


### Création d'un utilisateur de test HackerOne

Vous allez maintenant créer un utilisateur nommé Britta Simon dans HackerOne. HackerOne prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Lorsque vous accédez à HackerOne, un nouvel utilisateur est créé s'il n'existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Certify.




### Affectation de l’utilisateur de test Azure AD

Vous allez maintenant autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HackerOne.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à HackerOne, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **HackerOne**.

	![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

Pour terminer, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Quand vous cliquez sur la vignette HackerOne dans le volet d’accès, vous devez être connecté automatiquement à votre application HackerOne.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->