<properties
	pageTitle="Didacticiel : intégration d’Azure Active Directory à Workrite | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workrite."
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
	ms.date="10/16/2015"
	ms.author="markusvi"/>


# Didacticiel : intégration d’Azure Active Directory à Workrite

L’objectif de ce didacticiel est de vous montrer comment intégrer Workrite à Azure Active Directory (Azure AD).<br>L’intégration de Workrite à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Workrite. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Workrite (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD à Workrite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Workrite pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Workrite à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Workrite à partir de la galerie
Pour configurer l’intégration de Workrite à Azure AD, vous devez ajouter Workrite à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workrite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **Portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **Workrite**.<br><br> ![Applications][5]<br>
7. Dans le volet de résultats, sélectionnez **Workrite**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>![Applications][500]<br>


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Workrite au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Workrite équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Workrite associé doit être établie.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Workrite.
 
Pour configurer et tester l’authentification unique Azure AD avec Workrite, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Workrite](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Workrite lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Workrite.<br>

**Pour configurer l’authentification unique Azure AD avec Workrite, procédez comme suit :**

1. Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **Workrite**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Workrite**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][7]<br>

3. Dans la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Authentification unique Azure AD][8] <br>
 
     a. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre site Workrite (par ex. : **https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*).

     >[AZURE.NOTE]Contactez votre équipe de support Workrite [support@workrite.co.uk](mailto:support@workrite.co.uk) si vous ignorez la valeur de l’URL d’authentification.

     b. Cliquez sur **Next**.
 
4. Dans la page **Configurer l’authentification unique sur Workrite**, procédez comme suit : <br><br>![Authentification unique Azure AD][9] <br>

    a. Cliquez sur Télécharger le certificat, puis enregistrez le fichier sur votre ordinateur.

    b. Contacter votre équipe de support Workrite ([support@workrite.co.uk](mailto:support@workrite.co.uk)), fournissez-leur le certificat téléchargé, l’**URL de l’émetteur** (ID d’entité), l’**URL du service d’authentification unique**, l’**URL de déconnexion unique**, et demandez-leur de configurer l’authentification unique pour votre application Workrite.

    c. Cliquez sur **Next**.


6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. À la page **Confirmation de l'authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation **gauche du portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png) <br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png) <br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png) <br>

5. Dans la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, saisissez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png) <br>
 
    a. Dans la zone de texte **Prénom**, tapez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, saisissez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png) <br>
 
8. Dans la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png) <br>
  
    a. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test Workrite

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Workrite.

**Pour créer un utilisateur appelé Britta Simon dans Workrite, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Workrite en tant qu’administrateur.

2. Dans le volet de navigation, cliquez sur **Admin**. <br><br>![Affecter des utilisateurs][400]<br>

3. Accédez à Quick Links, puis cliquez sur **Create User**. <br><br>![Affecter des utilisateurs][401]<br>

4. Dans la boîte de dialogue **Create User**, procédez comme suit : <br><br>![Affecter des utilisateurs][402]<br>

    a. Tapez l’**adresse de messagerie**, le **prénom** et le **nom** d’un utilisateur Azure AD valide que vous voulez approvisionner.

    b. Sélectionnez **Client Administrator** pour **Choose Role**.

    c. Cliquez sur **Enregistrer**.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Workrite. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à Workrite, procédez comme suit :**

1. Dans la vue d’annuaire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>
2. Dans la liste des applications, sélectionnez **Workrite**. <br><br>![Affecter des utilisateurs][202] <br>
1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>
1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du panneau d’accès.<br> Quand vous cliquez sur la vignette Workrite dans le volet d’accès, vous devez être connecté automatiquement à votre application Workrite.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png

<!---HONumber=Oct15_HO4-->