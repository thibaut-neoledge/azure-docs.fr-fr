<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Novatus | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et HackerOne."
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
	ms.date="03/30/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à HackerOne

Dans ce didacticiel, vous intégrerez HackerOne à Azure Active Directory (Azure AD).

L’intégration de HackerOne à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à HackerOne.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à HackerOne (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à HackerOne, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement HackerOne pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de HackerOne à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de HackerOne à partir de la galerie
Pour intégrer HackerOne à Azure AD, vous devez ajouter HackerOne, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter HackerOne à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **HackerOne**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)<br>
7. Dans le volet de résultats, sélectionnez **HackerOne**, puis cliquez sur **Terminer** pour ajouter l’application.<br><br>


##  Configuration et test de l’authentification unique Azure AD
Ensuite, vous configurez et testez Azure AD l'authentification unique avec HackerOne basée sur un utilisateur de test appelé "Britta Simon".

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HackerOne équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur HackerOne associé doit être établie.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans HackerOne.

Pour configurer et tester l’authentification unique Azure AD avec HackerOne, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test HackerOne](#creating-a-hackerone-test-user)** pour avoir un équivalent de Britta Simon dans Certify lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Vous allez maintenant activer l’authentification unique Azure AD dans le portail Classic et configurer l’authentification unique dans votre application HackerOne.

**Pour configurer l’authentification unique Azure AD avec HackerOne, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **HackerOne**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à HackerOne**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) <br>

3. Dans la boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit, puis cliquez sur **Suivant** : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) <br>


    a. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application HackerOne, au format suivant : **"https://hackerone.com/nomentreprise/authentication"**. Lors du référencement d’un nom générique, la partie **nomentreprise** doit être remplacée par un nom réel.<br>

	b. Dans la zone de texte IDENTIFIER, tapez l'URL du locataire. Veuillez contacter l'équipe de support HackerOne via support@hackerone.com pour obtenir l'URL du locataire.

	c. Cliquez sur **Suivant**


4. Dans la boîte de dialogue **Configurer l'authentification unique sur HackerOne**, procédez comme suit, puis cliquez sur **Suivant**: <br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) <br>

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour obtenir l'authentification unique configurée pour votre application, vous devez vous connecter au locataire HackerOne en tant qu'utilisateur admin.
	
	a. Accédez au tableau de bord et cliquez sur l'option **Paramètres** le coin droit supérieur de la page.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) <br>

	b. Accédez à **Authentication** et cliquez sur le bouton "**Add SAML settings**".<br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) <br>

	c. Remplissez le formulaire **SAML Settings**.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) <br><br> c1. Entrez un domaine enregistré dans la zone de texte **Email Domain**. Pour enregistrer le domaine HackerOne, contactez support@hackerone.com.<br><br> c2. Copiez l'URL du service d'authentification unique Azure AD dans la zone **Single Sign On URL** de HackerOne.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_006.png) <br> c3. Convertissez le certificat téléchargé en un fichier base64, ouvrez-le dans Bloc-notes, puis copiez-collez-le dans la zone de texte **Certificat X509** de HackerOne.<br>

	d. Cliquez sur le bouton **Save**.<br><br> e. Cliquez sur le bouton **Run test** et assurez-vous que le test fonctionne. Si tel est le cas, demandez à l'équipe de support HackerOne de vérifier vos paramètres SAML via support@hackerone.com afin qu'elle les approuve.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) <br><br>


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br>![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
Vous allez maintenant créer un utilisateur de test appelé Britta Simon dans le portail Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test SECURE DELIVER dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) <br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) <br>

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) <br>

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) <br>

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) <br>

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) <br>

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.


### Création d'un utilisateur de test HackerOne

Vous allez maintenant créer un utilisateur nommé Britta Simon dans HackerOne. HackerOne prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Lorsque vous accédez à HackerOne, un nouvel utilisateur est créé s'il n'existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Certify.




### Affectation de l’utilisateur de test Azure AD

Vous allez maintenant autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HackerOne. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à HackerOne, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **HackerOne**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

Pour terminer, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Quand vous cliquez sur la vignette HackerOne dans le volet d’accès, vous devez être connecté automatiquement à votre application HackerOne.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0406_2016-->