<properties
	pageTitle="Didacticiel : Intégration de Pluralsight à Azure Active Directory | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pluralsight."
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
	ms.date="03/21/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration de Pluralsight à Azure Active Directory

L’objectif de ce didacticiel est de vous montrer comment intégrer Pluralsight à Azure Active Directory (Azure AD).<br>L’intégration de Pluralsight à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Pluralsight.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Pluralsight (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.


Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration de Pluralsight à Azure AD, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Pluralsight pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Pluralsight à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Pluralsight à partir de la galerie
Pour configurer l’intégration de Pluralsight à Azure AD, vous devez ajouter Pluralsight depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Pluralsight à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **Pluralsight**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)<br>
7. Dans le volet de résultats, sélectionnez **Pluralsight**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Pluralsight au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Pluralsight équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Pluralsight associé.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Pluralsight.

Pour configurer et tester l’authentification unique Azure AD avec Pluralsight, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Pluralsight](#creating-a-pluralsight-test-user)** pour avoir un équivalent de Britta Simon dans Pluralsight lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Pluralsight.

Votre application Pluralsight s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre un exemple : Supprimez les revendications par défaut de givenname, surname et emailaddress. Ajoutez les déclarations suivantes, puis sélectionnez les valeurs dans la liste déroulante comme indiqué. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) <br>

Vous pouvez également ajouter l’attribut **Unique ID** avec la valeur appropriée, comme EmployeeID ou autre valeur convenant à votre entreprise. Notez qu’il ne s’agit pas de l’attribut nécessaire, mais qu’il peut être ajouté pour l’identification de l’utilisateur unique.

**Pour configurer l’authentification unique Azure AD avec Pluralsight, procédez comme suit :**

1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Pluralsight**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Pluralsight**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png)<br>

3. Dans la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) <br>


    a. Dans la zone de texte URL d’authentification, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Pluralsight, au format suivant : **https://<nom\_instance>.pluralsight.com/sso/<nom\_entreprise>**.

    b. Cliquez sur **Next**.


4. Dans la page **Configurer l’authentification unique sur Pluralsight**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png) <br>

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez votre représentant Pluralsight et envoyez-lui le fichier de métadonnées téléchargé.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][11]



### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) <br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) <br>

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) <br>

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) <br>

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) <br>

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) <br>

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Pluralsight

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Pluralsight. Contactez l’équipe du support technique Pluralsight pour ajouter des utilisateurs au compte Pluralsight.


> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Pluralsight.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Pluralsight. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à Pluralsight, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **Pluralsight**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Quand vous cliquez sur la vignette Pluralsight dans le volet d’accès, vous êtes connecté automatiquement à votre application Pluralsight.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0323_2016-->