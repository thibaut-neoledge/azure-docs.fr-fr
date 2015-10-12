<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS) | Microsoft Azure"
	description="Découvrez comment utiliser Amazon Web Services (AWS) avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
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
	ms.date="09/30/2015"
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

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Active Directory][1]<br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Applications][2]<br>

4. Cliquez sur **Ajouter** en bas de la page. <br><br>![Applications][3]<br>

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**. <br><br>![Applications][4]<br>

6. Dans la zone de recherche, tapez **Amazon Web Service (AWS)**. <br><br>![Applications][5]<br>

7. Dans le volet des résultats, sélectionnez **Amazon Web Service (AWS)**, puis cliquez sur **Terminé** pour ajouter l’application. <br><br>![Applications][6]<br>



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Amazon Web Service (AWS) avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Amazon Web Service (AWS) équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Amazon Web Service (AWS) associé doit être établi.<br> Pour cela, attribuez la valeur du **nom d’utilisateur** dans Azure AD au **nom d’utilisateur** dans Amazon Web Service (AWS).
 
Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Service (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre aux utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Amazon Web Service (AWS) lié à sa représentation dans Azure AD.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Amazon Web Service (AWS).<br> Votre application Amazon Web Service (AWS) attend les mêmes assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des **attributs du jeton SAML**. La capture d’écran suivante montre un exemple :


<br><br>![Configurer l’authentification unique][27]<br>

**Pour configurer l’authentification unique Azure AD avec Amazon Web Service (AWS), procédez comme suit :**

1. Dans le portail Azure AD, dans la page d’intégration d’application **Amazon Web Service (AWS)**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br>![Configurer l’authentification unique][7]<br>

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Amazon Web Service (AWS)**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br>![Configurer l’authentification unique][8]<br>

3. Dans la page **Configurer les paramètres de l’application**, cliquez sur Suivant. <br><br>![Configurer les paramètres d’application][9]<br>
 
4. Dans la page **Configurer l’authentification unique sur Amazon Web Service (AWS)**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur. <br><br>![Configurer l’authentification unique][10]<br>

5. Dans une autre fenêtre de navigateur, connectez-vous au site de votre entreprise Amazon Web Service (AWS) en tant qu’administrateur.

6. Cliquez sur **Console Home**. <br><br>![Configurer l’authentification unique][11]<br>

7. Cliquez sur **Identity and Access Management**. <br><br>![Configurer l’authentification unique][12]<br>

8. Cliquez sur **Identity Providers**, puis sur **Create Provider**. <br><br>![Configurer l’authentification unique][13]<br>

9. Dans la page **Configure Provider**, procédez comme suit : <br><br>![Configurer l’authentification unique][14]<br>

     a. Pour **Provider Type**, sélectionnez **SAML**.

     b. Dans la zone de texte **Provider Name**, tapez le nom d’un fournisseur (par ex. : *WAAD*).

     c. Pour télécharger votre fichier de métadonnées, cliquez sur **Choose File**.

     d. Cliquez sur **Next Step**.


10. Dans la page **Verify Provider Information**, cliquez sur **Create**. <br><br>![Configurer l’authentification unique][15]<br>

11. Cliquez sur **Roles**, puis sur **Create New Role**. <br><br>![Configurer l’authentification unique][16]<br>

12. Dans la boîte de dialogue **Set Role Name**, procédez comme suit : <br><br>![Configurer l’authentification unique][17]<br>

     a. Dans la zone de texte **Role Name**, tapez un nom de rôle (par ex. : *Utilisateur\_test*).

     b. Cliquez sur **Next Step**.

13. Dans la boîte de dialogue **Select Role Type**, procédez comme suit : <br><br>![Configurer l’authentification unique][18]<br>

     a. Sélectionnez **Role For Identity Provider Access**.

     b. Dans la section **Grant Web Single Sign-On (WebSSO) access to SAML providers**, cliquez sur **Select**.


14. Dans la boîte de dialogue **Establish Trust**, procédez comme suit : <br><br>![Configurer l’authentification unique][19]<br>
     
     a. Pour le fournisseur SAML, sélectionnez celui que vous avez déjà créé (par ex. : *WAAD*)

     b. Cliquez sur **Next Step**.


15. Dans la boîte de dialogue **Verify Role Trust**, cliquez sur **Next Step**. <br><br>![Configurer l’authentification unique][32]<br>


16. Dans la boîte de dialogue **Attach Policy**, cliquez sur **Next Step**. <br><br>![Configurer l’authentification unique][33]<br>


17. Dans la boîte de dialogue **Review**, procédez comme suit : <br><br>![Configurer l’authentification unique][34]<br>

     a. Copiez la valeur du champ **Role ARN**.

     b. Copiez la valeur du champ **Trusted Entities ARN**.

     c. Cliquez sur **Create Role**.

18. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][20]<br>

19. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminé** pour fermer la boîte de dialogue **Configurer l’authentification unique**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][22]<br>


20. Dans le menu du haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**. <br><br>![Configurer l’authentification unique][21]<br>

21. Cliquez sur **ajouter un attribut utilisateur**. <br><br>![Configurer l’authentification unique][23]<br>

22. Dans la boîte de dialogue Ajouter un attribut utilisateur, procédez comme suit. <br><br>![Configurer l’authentification unique][24]<br>

     a. Dans la zone de texte **Nom d’attribut**, tapez ****https://aws.amazon.com/SAML/Attributes/Role**.

     b. Dans la zone de texte **Valeur d’attribut**, tapez **[valeur du champ Role ARN] [valeur du champ Trusted Entity ARN]**.

     >[AZURE.TIP]Ce sont les valeurs que vous avez copiées dans la boîte de dialogue Review quand vous avez créé votre rôle.

     c. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.

23. Cliquez sur **ajouter un attribut utilisateur**. <br><br>![Configurer l’authentification unique][23]<br>


24. Dans la boîte de dialogue Ajouter un attribut utilisateur, procédez comme suit. <br><br>![Configurer l’authentification unique][25]<br>


     a. Dans la zone de texte **Nom d’attribut**, tapez ****https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Dans la zone de texte **Valeur d’attribut**, tapez **mail**.

     c. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.


25. Cliquez sur **Appliquer les modifications**. <br><br>![Configurer l’authentification unique][26]<br>





### Création d’un utilisateur de test Azure AD

Cette section vous permet de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**.<br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils du bas. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. Dans la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>

  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, tapez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Dans la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>

  1. Dans la zone de texte **Prénom**, tapez **Britta**.
  2. Dans la zone de texte **Nom**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, tapez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Suivant**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **créer**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. Dans la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>

  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   
  
 
### Création d'un utilisateur de test Amazon Web Service (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS).

### Pour créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS), procédez comme suit :

1. Connectez-vous au site de votre entreprise **Amazon Web Service (AWS)** en tant qu’administrateur.

2. Cliquez sur l’icône **Console Home**. <br><br>![Configurer l’authentification unique][11]<br>

3. Cliquez sur Identity and Access Management. <br><br>![Configurer l’authentification unique][28]<br>

4. Dans le tableau de bord, cliquez sur Users, puis sur Create New Users. <br><br>![Configurer l’authentification unique][29]<br>

5. Dans la boîte de dialogue Create User, procédez comme suit : <br><br>![Configurer l’authentification unique][30]<br>

     a. Dans les zones de texte **Enter User Names**, tapez le nom d’utilisateur de Britta Simon dans Azure AD.

     b. Cliquez sur **Create**.




### Affectation de l’utilisateur de test Azure AD

Cette section décrit comment permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Amazon Web Service (AWS).

![Affecter des utilisateurs][31]

**Pour attribuer Britta Simon à CloudPassage, procédez comme suit :**

1. Dans la vue de répertoire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][26]<br>

2. Dans la liste des applications, sélectionnez **Amazon Web Service (AWS)**. <br><br>![Affecter des utilisateurs][27]<br>

1. Dans le menu du haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][25]<br>

1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][29]<br>

### Test de l’authentification unique

Cette section vous permet de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette Amazon Web Service (AWS) dans le volet d’accès, vous devez être connecté automatiquement à votre application Amazon Web Service (AWS).


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

<!---HONumber=Oct15_HO1-->