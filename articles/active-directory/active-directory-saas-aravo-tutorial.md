<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Aravo | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Aravo."
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
	ms.date="07/26/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec Aravo

L’objectif de ce didacticiel est de vous montrer comment intégrer Aravo avec Azure Active Directory (Azure AD).

L’intégration d’Aravo avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Aravo.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Aravo (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise

Pour configurer l’intégration d’Azure AD avec Aravo, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Aravo pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Aravo à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout d’Aravo à partir de la galerie
Pour configurer l’intégration d’Aravo avec Azure AD, vous devez ajouter Aravo, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Aravo à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
	
	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Aravo**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/tutorial_aravo_01.png)
7. Dans le volet des résultats, sélectionnez **Aravo**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Sélection de l’application dans la galerie](./media/active-directory-saas-aravo-tutorial/tutorial_aravo_0001.png)


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Aravo avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Aravo équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Aravo associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Aravo.

Pour configurer et tester l’authentification unique Azure AD avec Aravo, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Aravo](#creating-a-aravo-test-user)** pour avoir un équivalent de Britta Simon dans Aravo, lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Aravo.

**Pour configurer l’authentification unique Azure AD avec Aravo, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Aravo**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Aravo**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-aravo-tutorial/tutorial_aravo_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-aravo-tutorial/tutorial_aravo_04.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.aravo.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<company name>.aravo.com/aems/login.do`

	c. Cliquez sur **Suivant**

	> [AZURE.NOTE] Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour les valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez Aravo.

4. Dans la page **Configurer l’authentification unique sur Aravo**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-aravo-tutorial/tutorial_aravo_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.

5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Aravo et fournissez-lui les éléments suivants :

	-Le fichier de **certificat téléchargé**

	-**L’URL de l’émetteur**

	-**L’URL d’authentification unique SAML**

	-**L’URL du service de déconnexion unique**

6. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Authentification unique Azure AD][11]



### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.
	
![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_07.png)

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-aravo-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Aravo

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Aravo. Veuillez contacter l’équipe de support Aravo pour ajouter les utilisateurs dans le compte Aravo.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Aravo.
	
![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Aravo, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu supérieur.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Aravo**.

	![Configurer l’authentification unique](./media/active-directory-saas-aravo-tutorial/tutorial_aravo_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
	
	![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Aravo dans le volet d’accès, vous devez être connecté automatiquement à votre application Aravo.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-aravo-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0727_2016-->