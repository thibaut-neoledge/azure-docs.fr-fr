<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec RedVector | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et RedVector."
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
	ms.date="08/11/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure AD à RedVector

Dans ce didacticiel, vous allez apprendre à intégrer RedVector avec Azure Active Directory (Azure AD).

L’intégration de RedVector à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à RedVector
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à RedVector (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à RedVector, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement **RedVector** pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de RedVector à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de RedVector à partir de la galerie
Pour configurer l’intégration de RedVector avec Azure AD, vous devez ajouter RedVector, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter RedVector à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **RedVector**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_01.png)

7. Dans le volet des résultats, sélectionnez **RedVector**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_02.png)

##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec RedVector, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur RedVector équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur RedVector associé doit être établie. Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans RedVector.

Pour configurer et tester l’authentification unique Azure AD avec RedVector, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test RedVector](#creating-a-RedVector-test-user)** pour avoir un équivalent de Britta Simon dans RedVector lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application RedVector.


**Pour configurer l’authentification unique Azure AD avec RedVector, procédez comme suit :**

1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.

	![Configurer l’authentification unique][6]

2. Dans le portail Azure Classic, sur la page d’intégration d’applications **RedVector**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][7]

3. Dans la page **Comment voulez-vous que les utilisateurs se connectent à RedVector**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 	
	![Configurer l’authentification unique](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_06.png)

4. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_07.png)


    a. Dans la zone de texte URL de connexion, tapez une URL au format suivant : `https://sso2.redvector.com/adfs/<Companyname>`. Vous devrez peut-être contacter le support Redvector à l’adresse <sso@redvector.com> pour obtenir les valeurs correctes pour votre environnement.

	b. Cliquez sur **Next**.

5. Dans la page **Configurer l’authentification unique sur RedVector**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. Copiez également la valeur de l’URL du service d’authentification unique. Vous devez partager ces informations avec le support RedVector pour configurer l’authentification unique.

	![Configurer l’authentification unique](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_08.png)

6. Pour que l’authentification unique soit configurée pour votre application, contactez l’équipe du support technique RedVector à l’adresse <sso@redvector.com>. Ils vous aideront à configurer l’authentification unique à l’aide du canal approprié. Dans l’e-mail, fournissez-leur les éléments suivants :

	- Certificat téléchargé
	- **L’URL d’authentification unique SAML**

7. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
	
	![Authentification unique Azure AD][10]

8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
  	
	![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-redvector-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test RedVector

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans RedVector. Si vous ne savez pas comment ajouter Britta Simon dans RedVector, rapprochez-vous de l’équipe de support RedVector pour ajouter l’utilisateur de test et activer l’authentification unique. Contactez-la à l’adresse <sso@redvector.com>.

### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RedVector.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à RedVector, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **RedVector**.

	![Configurer l’authentification unique](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_09.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]


### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque RedVector dans le volet d’accès, vous devez être connecté automatiquement à votre application RedVector.

## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->