<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec CS Stars | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et CS Stars."
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


# Didacticiel : Intégration d’Azure Active Directory avec CS Stars

L’objectif de ce didacticiel est de vous montrer comment intégrer CS Stars dans Azure Active Directory (Azure AD). L’intégration de CS Stars dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à CS Stars.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à CS Stars (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Conditions préalables 

Pour configurer l’intégration d’Azure AD avec CS Stars, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement CS Stars pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de CS Stars à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de CS Stars à partir de la galerie
Pour configurer l’intégration de CS Stars avec Azure AD, vous devez ajouter CS Stars disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter CS Stars à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, saisissez **CS Stars**.

	![Applications][5]

7. Dans le volet de résultats, sélectionnez **CS Stars**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Applications][400]



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec CS Stars avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur CS Stars équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur CS Stars associé doit être établi. Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans CS Stars.
 
Pour configurer et tester l’authentification unique Azure AD avec CS Stars, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test CS Stars](#creating-a-cs-stars-test-user)** : pour avoir un équivalent de Britta Simon dans CS Stars lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le Portail Azure Classic et de configurer l’authentification unique dans votre application CS Stars.

**Pour configurer l’authentification unique Azure AD avec CS Stars, procédez comme suit :**

1. Dans le Portail Azure Classic, sur la page d’intégration d’applications **CS Stars**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à CS Stars**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][7]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer les paramètres d’application][8]
 
     3\.1. dans la zone de texte **URL d’authentification**, saisissez l’URL que vos utilisateurs utilisent pour se connecter à votre application CS Stars (par exemple : `https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2`).

     > [AZURE.NOTE] Si vous ne connaissez pas la valeur exacte, contactez votre représentant Marsh ClearSight.

     3\.2. Cliquez sur **Suivant**.
 
4. Dans la page **Configurer l’authentification unique sur CS Stars**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

	![Qu’est-ce qu’Azure AD Connect ?][9]

5. Pour activer l’authentification unique pour CS Stars, contactez votre représentant Marsh ClearSigh et remettez le fichier de métadonnées.


6. Dans le Portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Qu’est-ce qu’Azure AD Connect ?][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Qu’est-ce qu’Azure AD Connect ?][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)

	a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

	b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

	c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)

	a. Dans la zone de texte **Prénom**, entrez **Britta**.
  
	b. Dans la zone de texte **Nom**, saisissez **Simon**.
  
	c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  
	d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  
	e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)
  
	a. Notez la valeur du **Nouveau mot de passe**.
  
	b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test CS Stars

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans CS Stars.

Pour obtenir un utilisateur créé dans CS Stars, vous devez contacter votre représentant Marsh ClearSight.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à CS Stars.

![Affecter des utilisateurs][200]


**Pour affecter Britta Simon à CS Stars, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le Portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **CS Stars**.

	![Affecter des utilisateurs][202]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.


2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette CS Stars dans le volet d’accès, vous devez être connecté automatiquement à votre application CS Stars.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png

<!---HONumber=AcomDC_0817_2016-->