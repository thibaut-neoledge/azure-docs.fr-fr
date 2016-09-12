<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS) | Microsoft Azure"
	description="Découvrez comment utiliser Amazon Web Services (AWS) avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
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
	ms.date="06/06/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS)

L’objectif de ce didacticiel est de vous montrer comment intégrer Amazon Web Service (AWS) dans Azure Active Directory (Azure AD). L’intégration de Amazon Web Service (AWS) dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Service (AWS).
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Amazon Web Service (AWS) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Conditions préalables 

Pour configurer l’intégration d’Azure AD avec Amazon Web Service (AWS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Amazon Web Service (AWS) avec authentification unique


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d'Amazon Web Service (AWS) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout d'Amazon Web Service (AWS) à partir de la galerie
Pour configurer l’intégration d’Amazon Web Service (AWS) avec Azure AD, vous devez ajouter Amazon Web Service (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

### Pour ajouter Amazon Web Service (AWS) à partir de la galerie, procédez comme suit :

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
   
	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
	![Applications][4]

6. Dans la zone de recherche, tapez **Amazon Web Service (AWS)**.
   
	![Applications][5]

7. Dans le volet des résultats, sélectionnez **Amazon Web Service (AWS)**, puis cliquez sur **Terminé** pour ajouter l’application.

	![Applications][6]



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Amazon Web Service (AWS) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Amazon Web Service (AWS) équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Amazon Web Service (AWS) associé doit être établie. Pour cela, attribuez la valeur du **nom d’utilisateur** dans Azure AD au **nom d’utilisateur** dans Amazon Web Service (AWS).
 
Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Service (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre aux utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Amazon Web Service (AWS) lié à sa représentation dans Azure AD.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Amazon Web Service (AWS). Votre application Amazon Web Service (AWS) attend les mêmes assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des **attributs du jeton SAML**. La capture d’écran suivante montre un exemple :


![Configurer l’authentification unique][27]

**Pour configurer l’authentification unique Azure AD avec Amazon Web Service (AWS), procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Amazon Web Service (AWS)**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][7]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Amazon Web Service (AWS)**, sélectionnez **Authentification unique Azure AD**, puis sur **Suivant**.

	![Configurer l’authentification unique][8]

3. Dans la page **Configurer les paramètres de l’application**, cliquez sur Suivant.

	![Configurer les paramètres d’application][9]
 
4. Dans la page **Configurer l’authentification unique sur Amazon Web Service (AWS)**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

	![Configurer l’authentification unique][10]

5. Dans une autre fenêtre de navigateur, connectez-vous au site de votre entreprise Amazon Web Service (AWS) en tant qu’administrateur.

6. Cliquez sur **Console Home**.

	![Configurer l’authentification unique][11]

7. Cliquez sur **Gestion de l’identité et de l’accès**.

	![Configurer l’authentification unique][12]

8. Cliquez sur **Fournisseurs d’identité**, puis sur **Créer fournisseur**.

	![Configurer l’authentification unique][13]

9. Dans la page **Configure Provider**, procédez comme suit :

	![Configurer l’authentification unique][14]

     a. Pour **Provider Type**, sélectionnez **SAML**.

     b. Dans la zone de texte **Provider Name**, tapez le nom d’un fournisseur (par ex. : *WAAD*).

     c. Pour télécharger votre fichier de métadonnées, cliquez sur **Choose File**.

     d. Cliquez sur **Next Step**.


10. Dans la page **Verify Provider Information**, cliquez sur **Create**.

	![Configurer l’authentification unique][15]

11. Cliquez sur **Roles**, puis sur **Create New Role**.

	![Configurer l’authentification unique][16]

12. Dans la boîte de dialogue **Set Role Name**, procédez comme suit :

	![Configurer l’authentification unique][17]

	a. Dans la zone de texte **Role Name**, tapez un nom de rôle (par ex. : *Utilisateur\_test*).

	b. Cliquez sur **Next Step**.

13. Dans la boîte de dialogue **Select Role Type**, procédez comme suit :

	![Configurer l’authentification unique][18]

    a. Sélectionnez **Role For Identity Provider Access**.

    b. Dans la section **Grant Web Single Sign-On (WebSSO) access to SAML providers**, cliquez sur **Select**.


14. Dans la boîte de dialogue **Establish Trust**, procédez comme suit :

	![Configurer l’authentification unique][19]
     
     a. Pour le fournisseur SAML, sélectionnez celui que vous avez déjà créé (par ex. : *WAAD*)

     b. Cliquez sur **Next Step**.


15. Dans la boîte de dialogue **Verify Role Trust**, cliquez sur **Next Step**.

	![Configurer l’authentification unique][32]


16. Dans la boîte de dialogue **Attach Policy**, cliquez sur **Next Step**.

	![Configurer l’authentification unique][33]


17. Dans la boîte de dialogue **Review**, procédez comme suit :

	![Configurer l’authentification unique][34]

     a. Copiez la valeur du champ **Role ARN**.

     b. Copiez la valeur du champ **Trusted Entities ARN**.

     c. Cliquez sur **Create Role**.

18. Dans le Portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Qu’est-ce qu’Azure AD Connect ?][20]

19. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminé** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

	![Qu’est-ce qu’Azure AD Connect ?][22]


20. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

	![Configurer l’authentification unique][21]

21. Cliquez sur **Ajouter un attribut utilisateur**.

	![Configurer l’authentification unique][23]

22. Dans la boîte de dialogue Ajouter un attribut utilisateur, procédez comme suit.

	![Configurer l’authentification unique][24]

    a. Dans la zone de texte **Nom d’attribut**, tapez **https://aws.amazon.com/SAML/Attributes/Role**.

    b. Dans la zone de texte **Valeur d’attribut**, tapez **[valeur du champ Role ARN] [valeur du champ Trusted Entity ARN]**.

    >[AZURE.TIP] Ce sont les valeurs que vous avez copiées dans la boîte de dialogue Review quand vous avez créé votre rôle.

    c. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.

23. Cliquez sur **Ajouter un attribut utilisateur**.

	![Configurer l’authentification unique][23]


24. Dans la boîte de dialogue Ajouter un attribut utilisateur, procédez comme suit.

	![Configurer l’authentification unique][25]


     a. Dans la zone de texte **Nom d’attribut**, tapez **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Dans la zone de texte **Valeur d’attribut **, tapez ou sélectionnez **user.userprincipalname** dans la liste déroulante.
     
	![Configurer l’authentification unique][35]
	

     c. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.


25. Cliquez sur **Appliquer les modifications**.

	![Configurer l’authentification unique][26]





### Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

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

  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, saisissez **BrittaSimon**.
  3. Cliquez sur Suivant.

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
  
 
### Création d'un utilisateur de test Amazon Web Service (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS).

### Pour créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS), procédez comme suit :

1. Connectez-vous au site de votre entreprise **Amazon Web Service (AWS)** en tant qu’administrateur.

2. Cliquez sur l’icône **Console Home**.

	![Configurer l’authentification unique][11]

3. Cliquez sur Identity and Access Management.

	![Configurer l’authentification unique][28]

4. Dans le tableau de bord, cliquez sur Users, puis sur Create New Users.

	![Configurer l’authentification unique][29]

5. Dans la boîte de dialogue Create User, procédez comme suit :

	![Configurer l’authentification unique][30]

     a. Dans les zones de texte **Entrer les noms d’utilisateur**, tapez le nom d’utilisateur de Brita Simon (userprincipalname) dans Azure AD.

     b. Cliquez sur **Create**.




### Affectation de l’utilisateur de test Azure AD

Cette section décrit comment permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Amazon Web Service (AWS).

![Affecter des utilisateurs][31]

**Pour attribuer Britta Simon à CloudPassage, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][26]

2. Dans la liste des applications, sélectionnez **Amazon Web Service (AWS)**.

	![Affecter des utilisateurs][27]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][25]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][29]

### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette Amazon Web Service (AWS) dans le volet d’accès, vous devez être connecté automatiquement à votre application Amazon Web Service (AWS).


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png

<!---HONumber=AcomDC_0831_2016-->