<properties
	pageTitle="Didacticiel : intégration d’Azure Active Directory à QuickHelp | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et QuickHelp."
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
	ms.date="08/16/2016"
	ms.author="jeedes"/>


# Didacticiel : intégration d’Azure Active Directory à QuickHelp

L’objectif de ce didacticiel est de vous montrer comment intégrer QuickHelp dans Azure Active Directory (Azure AD). L’intégration de QuickHelp dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à QuickHelp.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à QuickHelp (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD à QuickHelp, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement QuickHelp pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de QuickHelp à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de QuickHelp à partir de la galerie
Pour configurer l’intégration de QuickHelp à Azure AD, vous devez ajouter QuickHelp à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter QuickHelp à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **QuickHelp**.

	![Applications][5]

7. Dans le volet de résultats, sélectionnez **QuickHelp**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Applications][500]


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec QuickHelp au moyen d’un utilisateur de test appelé « Britta Simon ».


Pour configurer et tester l’authentification unique Azure AD avec QuickHelp, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test QuickHelp](#creating-a-quickhelp-test-user)** pour avoir un équivalent de Britta Simon dans QuickHelp lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le Portail Azure Classic et de configurer l’authentification unique dans votre application QuickHelp.


**Pour configurer l’authentification unique Azure AD avec QuickHelp, procédez comme suit :**

1. Dans le Portail Azure Classic, puis dans la page d’intégration d’applications **QuickHelp**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à QuickHelp**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][7]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer les paramètres d’application][8]
 
     a. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre site QuickHelp (par ex. : * https://quickhelp.com/bsiazure/*).

     > [AZURE.NOTE] Contactez votre équipe de support QuickHelp si vous ignorez la valeur de l’URL d’authentification.

     b. Cliquez sur **Next**.

 
4. Dans la page **Configurer l’authentification unique sur QuickHelp**, procédez comme suit : cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

	![Qu’est-ce qu’Azure AD Connect ?][9]



1. Connectez-vous à votre site d’entreprise QuickHelp en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Admin**.

	![Configurer l’authentification unique][21]


1. Dans le menu **QuickHelp Admin** (Administration de QuickHelp), cliquez sur **Settings** (Paramètres).

	![Configurer l’authentification unique][22]

1. Cliquez sur **Authentication Settings**.

1. Dans la page **Authentication Settings** (Paramètres d’authentification), procédez comme suit :

	![Configurer l’authentification unique][23]

    a. Pour **SSO Type**, sélectionnez **WSFederation**.

    b. Pour charger votre fichier de métadonnées Azure téléchargé, cliquez sur **Browse**, accédez au fichier, puis cliquez sur **Upload Metadata**.

    c. Dans la zone de texte **Adresse de messagerie**, entrez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. Dans la zone de texte **First Name**, **tapez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Dans la zone de texte **Last Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. Dans la **barre d’actions**, cliquez sur **Enregistrer**.







6. Dans le Portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Qu’est-ce qu’Azure AD Connect ?][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Qu’est-ce qu’Azure AD Connect ?][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png)
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png)
 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png)
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test QuickHelp

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans QuickHelp. Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur QuickHelp équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur QuickHelp associé doit être établie.

QuickHelp prend en charge l’approvisionnement juste-à-temps. Cela signifie que, si nécessaire, un compte d’utilisateur est automatiquement créé dans QuickHelp et qu’il est lié au compte Azure AD.

Vous n’avez aucune opération à effectuer dans cette section.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à QuickHelp.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à QuickHelp, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le Portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **QuickHelp**.

	![Affecter des utilisateurs][202]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Quand vous cliquez sur la vignette QuickHelp dans le volet d’accès, vous devez être connecté automatiquement à votre application QuickHelp.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=AcomDC_0817_2016-->