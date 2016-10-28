<properties
	pageTitle="Didacticiel : intégration d’Azure Active Directory à SciQuest Spend Director| Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et SciQuest Spend Director."
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
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec SciQuest Spend Director

L’objectif de ce didacticiel est de vous montrer comment intégrer SciQuest Spend Director à Azure Active Directory (Azure AD). L’intégration de SciQuest Spend Director à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SciQuest Spend Director.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SciQuest Spend Director (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec SciQuest Spend Director, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SciQuest Spend Director pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SciQuest Spend Director à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de SciQuest Spend Director à partir de la galerie
Pour configurer l’intégration de SciQuest Spend Director avec Azure AD, vous devez ajouter SciQuest Spend Director, qui est disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SciQuest Spend Director à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **sciQuest spend director**.

	![Applications][5]

7. Dans le volet des résultats, sélectionnez **SciQuest Spend Director**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Applications][6]


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec SciQuest Spend Director avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur SciQuest Spend Director équivalent à un utilisateur dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SciQuest Spend Director associé doit être établi. Pour cela, affectez la valeur de **Nom d’utilisateur** dans Azure AD comme valeur de **Username** dans SciQuest Spend Director.
 
Pour configurer et tester l’authentification unique Azure AD avec SciQuest Spend Director, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test SciQuest Spend Director](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans SciQuest Spend Director qui soit lié à la représentation de cette personne dans Azure AD.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application SciQuest Spend Director.

**Pour configurer l’authentification unique Azure AD avec SciQuest Spend Director, procédez comme suit :**

1. Dans la page d’intégration d’applications **SciQuest Spend Director** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][8]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SciQuest Spend Director**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][9]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer les paramètres d’application][10]
 
     3\.1. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SciQuest Spend Director, au format suivant : *https://.*sciquest.com/.**

     3\.2. Dans la zone de texte **URL de réponse**, tapez la valeur que vous venez de taper dans la zone de texte **URL d’authentification**.

     3\.3. Cliquez sur **Suivant**.
 
4. Dans la page **Configurer l’authentification unique sur SciQuest Spend Director**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

	![Qu’est-ce qu’Azure AD Connect ?][11]

5. Contactez le support technique de SciQuest pour activer cette méthode d’authentification à l’aide des métadonnées téléchargées ci-dessus.

6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

	![Qu’est-ce qu’Azure AD Connect ?][15]

10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Qu’est-ce qu’Azure AD Connect ?][100]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Qu’est-ce qu’Azure AD Connect ?][101]

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Qu’est-ce qu’Azure AD Connect ?][102]

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Qu’est-ce qu’Azure AD Connect ?][103]

	a. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.
  
	b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  
	c. Cliquez sur Suivant.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Qu’est-ce qu’Azure AD Connect ?][104]

	a. Dans la zone de texte **Prénom**, entrez **Britta**.
  
	b. Dans la zone de texte **Nom**, saisissez **Simon**.
  
	c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  
	d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  
	e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Qu’est-ce qu’Azure AD Connect ?][105]

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Qu’est-ce qu’Azure AD Connect ?][106]

	a. Notez la valeur du **Nouveau mot de passe**.
  
	b. Cliquez sur **Terminé**.
  
 
### Création d’un utilisateur de test SciQuest Spend Director

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SciQuest Spend Director.

Vous devez contacter votre équipe de support SciQuest Spend Director et leur fournir les détails relatifs au compte que vous voulez créer.

Sinon, vous pouvez également tirer parti de l’approvisionnement juste-à-temps, une fonctionnalité d’authentification unique prise en charge par SciQuest Spend Director. Si l’approvisionnement juste-à-temps est activé, SciQuest Spend Director crée les utilisateurs automatiquement, si nécessaire, lors d’une tentative d’authentification unique. Cette fonctionnalité élimine le besoin de créer manuellement les utilisateurs équivalents de l’authentification unique.

Pour que l’approvisionnement juste-à-temps soit activé, vous devez contacter votre équipe de support SciQuest Spend Director.
  

### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SciQuest Spend Director.

![Qu’est-ce qu’Azure AD Connect ?][200]

**Pour affecter Britta Simon à SciQuest Spend Director, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Qu’est-ce qu’Azure AD Connect ?][201]

2. Dans la liste des applications, sélectionnez **SciQuest Spend Director**.

	![Qu’est-ce qu’Azure AD Connect ?][202]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Qu’est-ce qu’Azure AD Connect ?][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

	![Qu’est-ce qu’Azure AD Connect ?][204]

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Qu’est-ce qu’Azure AD Connect ?][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette SciQuest Spend Director dans le volet d’accès, vous devez être connecté automatiquement à votre application SciQuest Spend Director.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!---HONumber=AcomDC_0907_2016-->