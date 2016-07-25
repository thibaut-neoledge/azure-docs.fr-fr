<properties
	pageTitle="Didacticiel : Intégration d’Azure AD à Flatter Files | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Flatter Files."
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
	ms.date="07/11/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Flatter Files

L’objectif de ce didacticiel est de vous montrer comment intégrer Flatter Files dans Azure AD (Azure Active Directory). L’intégration de Flatter Files dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Flatter Files.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Flatter Files (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD avec Flatter Files, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Flatter Files pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Flatter Files à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Flatter Files à partir de la galerie
Pour configurer l’intégration de Flatter Files avec Azure AD, vous devez ajouter Flatter Files à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Flatter Files à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **Flatter Files**.


	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. Dans le volet de résultats, sélectionnez **Flatter Files**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Flatter Files avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Flatter Files équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Flatter Files associé doit être établie. Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Flatter Files.
 
Pour configurer et tester l’authentification unique Azure AD avec Flatter Files, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Flatter Files](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Flatter Files lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD Classic et de configurer l’authentification unique dans votre application Flatter Files. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Pour configurer l’authentification unique pour Flatter Files, vous avez besoin d’un domaine enregistré. Si vous n’avez pas encore de domaine enregistré, contactez votre équipe de support Flatter Files via [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Pour configurer l’authentification unique Azure AD avec Flatter Files, procédez comme suit :**

1. Dans la page d’intégration d’applications **Flatter Files** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Flatter Files**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
	![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png)

3. Dans la page **Configurer les paramètres de l’application**, cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png)

    > [AZURE.NOTE] Flatter Files utilise la même URL de connexion d’authentification unique pour tous les clients : [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
 
 
4. Sur la page **Configurer l’authentification unique sur Flatter Files**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


1. Connectez-vous à votre application Flatter Files en tant qu’administrateur.

2. Cliquez sur Tableau de bord.

	![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)



2. Cliquez sur **Settings**, puis procédez comme suit dans l’onglet **Company** :

	![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)

    a. Sélectionnez **Use SAML 2.0 for Authentication**.

    b. Cliquez sur **Configure SAML**.



2. Dans la boîte de dialogue **SAML Configuration**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)

    a. Dans la zone de texte Domain, entrez votre domaine enregistré.

    > [AZURE.NOTE] Si vous n’avez pas encore de domaine enregistré, contactez votre équipe de support Flatter Files via [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. Dans la page Configurer l’authentification unique sur Flatter Files du portail Azure Classic, copiez l’URL du service d’authentification unique, puis collez-la dans la zone de texte URL de fournisseur d’identité.

    c. Créez un fichier **codé en base 64** à partir du certificat téléchargé.

    >[AZURE.TIP] Pour plus d’informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    d. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat de fournisseur d’identité Flatter Files**.

    e. Cliquez sur **Mettre à jour**.

6. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png)
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png)
 
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png)
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test Flatter Files

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Flatter Files.

**Pour créer un utilisateur appelé Britta Simon dans Flatter Files, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Flatter Files** en tant qu’administrateur.

2. Dans le volet de navigation situé à gauche, cliquez sur **Settings**, puis sur **l’onglet** Users.

	![Créer un utilisateur Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Cliquez sur **Add User**.

4. Dans la boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :

	![Créer un utilisateur Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Email Address**, tapez l’adresse de messagerie de Britta indiquée dans le portail Azure Classic.

    d. Cliquez sur **Envoyer**.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Flatter Files.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Flatter Files, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Flatter Files**.

	![Affecter des utilisateurs](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette Flatter Files dans le volet d’accès, vous devez être connecté automatiquement à votre application Flatter Files.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->