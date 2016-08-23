<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec 23 Video | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et 23 Video."
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
	ms.date="08/05/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à 23 Video

L’objectif de ce didacticiel est de vous montrer comment intégrer 23 Video avec Azure Active Directory (Azure AD). L’intégration de 23 Video dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à 23 Video.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à 23 Video (via l’authentification unique) avec leur compte Azure AD.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec 23 Video, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement 23 Video pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de 23 Video à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de 23 Video à partir de la galerie
Pour configurer l’intégration de 23 Video avec Azure AD, vous devez ajouter 23 Video, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter 23 Video à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **23 Video**.

	![Applications][5]

7. Dans le volet de résultats, sélectionnez **23 Video**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Applications][25]

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec 23 Video avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur 23 Video équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur 23 Video associé doit être établie. Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans 23 Video.
 
Pour configurer et tester l’authentification unique Azure AD avec 23 Video, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test 23 Video](#creating-a-23-video-test-user)** pour avoir un équivalent de Britta Simon dans 23 Video lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application 23 Video.

**Pour configurer l’authentification unique Azure AD avec 23 Video, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **23 Video**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à 23 Video**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][7]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Authentification unique Azure AD][8]
 
     a. Dans la zone de texte **URL de réponse**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre site 23 Video (par ex. : *https://britta-simon.23Video.com/saml/login*).

     > [AZURE.NOTE] L’intégration d’Active Directory à l’aide de SAML 2.0 est disponible pour tous les utilisateurs de 23 Video. Contactez le support technique à l’adresse [support@23company.com](mailto:support@23company.com) si vous avez besoin des métadonnées associées.

     b. Cliquez sur **Next**.
 
4. Dans la page **Configurer l’authentification unique sur 23 Video**, procédez comme suit :

	![Authentification unique Azure AD][9]

    a. Cliquez sur Télécharger le certificat, puis enregistrez le fichier sur votre ordinateur.

    b. Contactez votre équipe de support 23 Video à l’adresse [support@23company.com](mailto:support@23company.com), fournissez-leur le certificat téléchargé, l’**URL de l’émetteur**, l’**URL du service d’authentification unique**, l’**URL de déconnexion unique**, et demandez-leur de configurer l’authentification unique pour votre application 23 Video.

    c. Cliquez sur **Next**.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
  
	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png)
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png)
 
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png)
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test 23 Video

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans 23 Video.

**Pour créer un utilisateur appelé Britta Simon dans 23 Video, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise 23 Video en tant qu’administrateur.

1. Accédez à **Settings**.


2. Dans la section **Users**, cliquez sur **Configure**.

	![Affecter des utilisateurs][400]

3. Cliquez sur **Add a new user**.

	![Affecter des utilisateurs][401]

4. Dans la section **Invite someone to join this site**, procédez comme suit :

	![Affecter des utilisateurs][402]

    a. Dans la zone de texte **Adresses e-mail**, tapez l’adresse e-mail de Britta Simon dans Azure AD.

    b. Cliquez sur **Add the user**.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à 23 Video.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à 23 Video, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **23 Video**.

	![Affecter des utilisateurs][202]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette 23 Video dans le panneau d’accès, vous devez être connecté automatiquement à votre application 23 Video.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png

<!---HONumber=AcomDC_0810_2016-->