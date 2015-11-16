<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Kiteworks | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kiteworks."
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
	ms.date="11/05/2015"
	ms.author="markusvi"/>


# Didacticiel : Intégration d’Azure Active Directory à Kiteworks

L’objectif de ce didacticiel est de vous montrer comment intégrer Kiteworks avec Azure Active Directory (Azure AD).<br>L’intégration de Kiteworks avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Kiteworks. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Kiteworks (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD avec Kiteworks, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kiteworks pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kiteworks à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Kiteworks à partir de la galerie
Pour configurer l’intégration de Kiteworks avec Azure AD, vous devez ajouter Kiteworks à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Kiteworks à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, entrez **Kiteworks**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)<br>
7. Dans le volet de résultats, sélectionnez **Kiteworks**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)<br>

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Kiteworks avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Kiteworks équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Kiteworks associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Kiteworks.
 
Pour configurer et tester l’authentification unique Azure AD avec Kiteworks, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Kiteworks](#creating-a-kiteworks-test-user)** pour avoir un équivalent de Britta Simon dans Kiteworks lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Kiteworks. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Pour configurer l’authentification unique pour Kiteworks, vous avez besoin d’un domaine enregistré. Si vous n’avez pas encore de domaine enregistré, contactez votre équipe de support Kiteworks via [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Pour configurer l’authentification unique Azure AD avec Kiteworks, procédez comme suit :**

1. Dans le portail Azure AD, sur la page d’intégration d’application **Kiteworks**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Kiteworks**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) <br>

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) <br>


    a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application Kiteworks (p. ex., **https://fabrikam.kiteworks.com/*).

    b. Cliquez sur **Next**.
 
 
4. Sur la page **Configurer l’authentification unique sur Kiteworks**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png) <br>

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


1. Connectez-vous à votre site d’entreprise Kiteworks en tant qu’administrateur.

1. Dans la barre d’outils située en haut, cliquez sur **Settings**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) <br>


1. Dans la section **Authentication and Authorization**, cliquez sur **SSO Setup**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) <br>


1. Sur la page de configuration de l’authentification unique, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png) <br>

    a. Sélectionnez **S’authentifier via l’authentification unique**.

    b. Sélectionnez **Initier Demande d’authentification**.

    c. Dans le portail Azure, sur la page **Configurer l’authentification unique sur Kiteworks**, copiez la valeur **ID d’entité**, puis collez-la dans la zone de texte **ID d’entité du fournisseur d’identité**.

    d. Sur la page **Configurer l’authentification unique sur Kiteworks** du portail Azure, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL du service d’authentification unique**.

    e. Sur la page **Configurer l’authentification unique sur Kiteworks** du portail Azure, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL du service de déconnexion unique**.

    f. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat de clé publique RSA**.

    g. Cliquez sur **Enregistrer**.


6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png) <br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) <br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) <br>

5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) <br>
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, entrez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Sur la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) <br>
 
8. Sur la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) <br>
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test Kiteworks

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Kiteworks. Kiteworks prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Kiteworks s’il n’existe pas déjà.

> [AZURE.NOTE]Si vous devez créer un utilisateur manuellement, contactez l’équipe de support Kiteworks.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Kiteworks. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à Kiteworks, procédez comme suit :**

1. Dans la vue de répertoire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **Kiteworks**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette Kiteworks dans le volet d’accès, vous devez être connecté automatiquement à votre application Kiteworks.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO2-->