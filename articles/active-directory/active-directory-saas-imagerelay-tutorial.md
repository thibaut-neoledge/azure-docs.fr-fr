<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à ImageRelay | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et ImageRelay."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à ImageRelay

L'objectif de ce didacticiel est de vous montrer comment intégrer ImageRelay à Azure Active Directory (Azure AD).<br>L’intégration d'ImageRelay à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ImageRelay.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ImageRelay (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD avec ImageRelay, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ImageRelay pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d'ImageRelay à partir de la galerie

2. Configuration et test de l’authentification unique Azure AD


## Ajout d'ImageRelay à partir de la galerie
Pour configurer l’intégration d'ImageRelay avec Azure AD, vous devez ajouter ImageRelay à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ImageRelay à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **ImageRelay**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)<br>
7. Dans le volet des résultats, sélectionnez **ImageRelay**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)<br>

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec ImageRelay sur un utilisateur de test appelé « Britta Simon ».

Pour que l'authentification unique fonctionne, Azure AD a besoin d'un compte d'utilisateur qui représente l'utilisateur ImageRelay associé. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur ImageRelay associé doit être établie.<br> Pour cela, affectez la valeur de **Nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** dans ImageRelay.

Pour configurer et tester l’authentification unique Azure AD avec ImageRelay, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test ImageRelay](#creating-a-userecho-test-user)** pour avoir un équivalent de Britta Simon dans ImageRelay lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD Classic et de configurer l’authentification unique dans votre application ImageRelay.


**Pour configurer l’authentification unique Azure AD avec ImageRelay, procédez comme suit :**

1. Dans le portail Azure AD Classic, sur la page d’intégration d’application **ImageRelay**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

     ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à ImageRelay**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) <br>

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

     ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) <br>

    a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ImageRelay (p. ex., **https://fabrikam.ImageRelay.com/*).

    b. Cliquez sur **Next**.

4. Sur la page **Configurer l’authentification unique sur ImageRelay**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) <br>

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.

5. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise ImageRelay en tant qu’administrateur.

    a. Dans la barre d'outils du haut, cliquez sur la charge de travail **Utilisateurs et autorisations**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) <br>

    b. Cliquez sur **Créer une nouvelle autorisation**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)<br>

    c. Dans la charge de travail **Paramètres d'authentification unique**, sélectionnez la case à cocher **Ce groupe peut se connecter uniquement via l'authentification unique**, puis cliquez sur **Enregistrer**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) <br>

    d. Accédez à **Paramètres du compte**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) <br>

    e. Accédez à la charge de travail **Paramètres d'authentification unique**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)<br>

    f. Remplissez le formulaire comme indiqué, puis cliquez sur **Enregistrer**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)<br>

    - **URL de connexion (SSO)**: il s'agit de l'URL de service d'authentification unique à partir d'Azure Active Directory.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_13.png)<br>

    - **URL de service de déconnexion **: il s'agit du service d'authentification unique à partir d'Azure Active Directory.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_14.png)<br>

    - Sous **Format Name id**, sélectionnez **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_15.png)<br>

    - Sous **Options de liaison pour les demandes provenant du fournisseur de services (ImageRelay)**, sélectionnez **POST liaison**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_16.png)<br>

  	- Sous **Certificat x.509**, cliquez sur **Mettre à jour le certificat**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)<br>

    - Dans le bloc-notes, ouvrez le certificat téléchargé à partir d'Azure Active Directory à l'étape 4, puis copiez et collez le contenu du certificat ici.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)<br>

    - Dans **Attribution d'utilisateurs juste à temps**, sélectionnez la case à cocher **Activer l'affectation des utilisateurs juste à temps**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)<br>

    - Sélectionnez le groupe d'autorisations (par exemple, **SSO de base**) qui sera autorisé à se connecter uniquement via l'authentification unique.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)<br>

6. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Authentification unique Azure AD][10]<br>

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

    ![Authentification unique Azure AD][11]


### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure AD Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure AD Classic**, cliquez sur **Active Directory**.<br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) <br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) <br>

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) <br>

5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) <br>

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) <br>

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) <br>

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d'un utilisateur de test ImageRelay

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ImageRelay.

**Pour créer un utilisateur appelé Britta Simon dans ImageRelay, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise ImageRelay tant qu’administrateur.

1. Accédez à **Utilisateurs et autorisations** et sélectionnez **Créer un utilisateur SSO**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) <br>

1. Entrez **E-mail**, **Prénom**, **Nom** et **Société** de l'utilisateur que vous souhaitez affecter, puis sélectionnez le groupe d'autorisations (par exemple authentification unique de base), qui peut se connecter uniquement via l'authentification unique.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) <br>

1. Cliquez sur **Create**.

### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui donnant accès à ImageRelay. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à ImageRelay, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **ImageRelay**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]


### Test de l’authentification unique

L’objectif de cette section est de tester votre configuration d’authentification unique Azure AD avec le panneau d’accès.<br> Lorsque vous cliquez sur la mosaïque ImageRelay dans le panneau d’accès, vous devriez être connecté automatiquement à votre application ImageRelay.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->