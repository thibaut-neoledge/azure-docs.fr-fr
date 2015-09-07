<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS) | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Web Service (AWS)."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="markvi"/>


# Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS)

L’objectif de ce didacticiel est de vous montrer comment intégrer Amazon Web Service (AWS) avec Azure Active Directory (Azure AD).<br>L’intégration d’Amazon Web Service (AWS) avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Service (AWS). 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Amazon Web Service (AWS) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec Amazon Web Service (AWS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Amazon Web Service (AWS) avec authentification unique


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d'Amazon Web Service (AWS) à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout d'Amazon Web Service (AWS) à partir de la galerie
Pour configurer l’intégration d’Amazon Web Service (AWS) avec Azure AD, vous devez ajouter Amazon Web Service (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

### Pour ajouter Amazon Web Service (AWS) à partir de la galerie, procédez comme suit :

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]
6. Dans la zone de recherche, saisissez **Amazon Web Service (AWS)**.<br><br> ![Applications][5]
7. Dans le volet des résultats, sélectionnez **Amazon Web Service (AWS)**, puis cliquez sur **Terminé** pour ajouter l’application.<br><br> ![Applications][6]



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Amazon Web Service (AWS) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Amazon Web Service (AWS) équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Amazon Web Service (AWS) associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Amazon Web Service (AWS).
 
Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Service (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Amazon Web Service (AWS) qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Amazon Web Service (AWS).<br> Votre application Amazon Web Service (AWS) s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML**. La capture d’écran suivante montre un exemple. <br><br> ![Configurer l’authentification unique][27]

**Pour configurer l’authentification unique Azure AD avec Amazon Web Service (AWS), procédez comme suit :**

1. Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **Amazon Web Service (AWS)**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.<br><br> ![Configurer l’authentification unique][7]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Amazon Web Service (AWS)**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.<br><br> ![Configurer l’authentification unique][8]

3. Dans la page de dialogue **Configurer les paramètres de l’application**, cliquez sur Suivant. <br><br>![Configurer les paramètres d’application][9]
 
4. Dans la page **Configurer l’authentification unique sur Amazon Web Service (AWS)**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.<br><br>![Configurer l’authentification unique][10]

5. Dans une autre fenêtre de navigateur, connectez-vous au site de votre entreprise Amazon Web Service (AWS) en tant qu’administrateur.

6. Cliquez sur **Console Home**. <br><br> ![Configurer l’authentification unique][11]

7. Cliquez sur **Identity and Access Management**. <br><br> ![Configurer l’authentification unique][12]

8. Cliquez sur **Identity Providers**, puis sur **Create Provider**. <br><br> ![Configurer l’authentification unique][13]

9. Sur la page **Configure Provider**, procédez comme suit : <br><br>![Configurer l’authentification unique][14]

     9\.1. Pour **Provider Type**, sélectionnez **SAML**.

     9\.2. Dans la zone de texte **Provider Name**, tapez le nom d’un fournisseur (par ex. : *WAAD*).

     9\.3. Pour télécharger votre fichier de métadonnées, cliquez sur **Choose File**.

     9\.4. Cliquez sur **Next Step**.


10. Sur la page **Verify Provider Information**, cliquez sur **Create**. <br><br> ![Configurer l’authentification unique][15]

11. Accédez à **Roles > Create New Role**. <br><br> ![Configurer l’authentification unique][16]

12. Dans la boîte de dialogue **Set Role Name**, procédez comme suit : <br><br> ![Configurer l’authentification unique][17] 12.1. Dans la zone de texte **Role Name**, tapez un nom de rôle (par ex. : *TestUser*).

     12\.2. Cliquez sur **Next Step**.

13. Dans la boîte de dialogue **Select Role Type**, procédez comme suit : <br><br> ![Configurer l’authentification unique][18]

     13\.1. Sélectionnez **Role For Identity Provider Access**.

     13\.2. Dans la section **Grant Web Single Sign-On (WebSSO) access to SAML providers**, cliquez sur **Select**.


14. Dans la boîte de dialogue Establish Trust, procédez comme suit : <br><br> ![Configurer l’authentification unique][19] 14.1. En tant que fournisseur SAML, sélectionnez le fournisseur SAML que vous avez créé précédemment (par ex. : *WAAD*)



15. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l'authentification unique, puis cliquez sur **Next**.<br><br>![Qu’est-ce qu’Azure AD Connect ?][20]

16. Sur la page **Single sign-on confirmation**, cliquez sur **Terminer** pour fermer la boîte de dialogue **Configure single sign-on**.<br><br>![Qu’est-ce qu’Azure AD Connect ?][22]


17. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.<br><br> ![Configurer l’authentification unique][21]

18. Cliquez sur **Ajouter un attribut utilisateur**. <br><br> ![Configurer l’authentification unique][23]

19. Dans la boîte de dialogue Ajouter un attribut, procédez comme suit. <br><br> ![Configurer l’authentification unique][24]

     19\.1. Dans la zone de texte **Nom de l’attribut**, entrez ****https://aws.amazon.com/SAML/Attributes/Role**.

     19\.2. Dans la zone de texte **Valeur de l'attribut**, entrez **arn:aws:iam::214510765665:role/Admin,arn:aws:iam::214510765665:saml-provider/WAAD**.

     19\.3. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.

20. Cliquez sur **Ajouter un attribut utilisateur**. <br><br> ![Configurer l’authentification unique][23]


21. Dans la boîte de dialogue Ajouter un attribut, procédez comme suit. <br><br> ![Configurer l’authentification unique][25]

     21\.1. Dans la zone de texte **Nom de l’attribut**, entrez ****https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     21\.2. Dans la liste **Valeur de l'attribut**, sélectionnez **user:mail**.

     21\.3. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.


22. Cliquez sur **Appliquer les modifications**. <br><br> ![Configurer l’authentification unique][26]





### Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**.<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**.<br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, tapez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Display Name**, tapez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. Sur la page **Obtenir un mot de passe temporaire**, procédez comme suit :<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   
  
 
### Création d'un utilisateur de test Amazon Web Service (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS).

### Pour créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS), procédez comme suit :

1. Connectez-vous au site de votre entreprise **Amazon Web Service (AWS)** en tant qu’administrateur.

2. Cliquez sur l’icône **Console Home**. <br><br> ![Configurer l’authentification unique][11]

3. Cliquez sur Identity and Access Management. <br><br> ![Configurer l’authentification unique][28]

4. Dans le tableau de bord, cliquez sur Users, puis cliquez sur Create New Users. <br><br> ![Configurer l’authentification unique][29]

5. Dans la boîte de dialogue Create User, procédez comme suit : <br><br> ![Configurer l’authentification unique][30]

     5\.1. Dans les zones de texte **Enter User Names**, entrez le nom d'utilisateur de Simon Britta dans Azure AD.

     5\.2. Cliquez sur **Create**.




### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Amazon Web Service (AWS).<br><br>![Affecter des utilisateurs][31]

**Pour attribuer Britta Simon à CloudPassage, procédez comme suit :**

1. Dans la vue d’annuaire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu supérieur.<br> <br><br>![Affecter des utilisateurs][26]
2. Dans la liste des applications, sélectionnez **Amazon Web Service (AWS)**. <br><br>![Affecter des utilisateurs][27]
1. Dans le menu situé en haut, cliquez sur **Users**.<br> <br><br>![Affecter des utilisateurs][25]
1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][29]

### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du panneau d’accès.<br> Lorsque vous cliquez sur la vignette Amazon Web Service (AWS) dans le volet d’accès, vous devez être connecté automatiquement à votre application Amazon Web Service (AWS).


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

<!---HONumber=August15_HO9-->