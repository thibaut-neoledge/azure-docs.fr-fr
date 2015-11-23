<properties
	pageTitle="Didacticiel : Intégration d'Azure Active Directory avec Moxtra | Microsoft Azure"
	description="Découvrez comment configurer l'authentification unique entre Azure Active Directory et Moxtra."
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


# Didacticiel : Intégration d'Azure Active Directory avec Moxtra

L'objectif de ce didacticiel est de vous montrer comment intégrer Moxtra avec Azure Active Directory (Azure AD).<br>L'intégration de Moxtra avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Moxtra 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Moxtra (via l'authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l'intégration d'Azure AD avec Moxtra, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Moxtra pour lequel l'authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Moxtra à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Moxtra à partir de la galerie
Pour configurer l'intégration de Moxtra à Azure AD, vous devez ajouter Moxtra à votre liste d'applications SaaS gérées à partir de la galerie.

**Pour ajouter Moxtra à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **Portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **Moxtra**.<br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. Dans le volet de résultats, sélectionnez **Moxtra**, puis cliquez sur **Terminer** pour ajouter l'application. <br><br>

##  Configuration et test de l’authentification unique Azure AD
L'objectif de cette section est de vous montrer comment configurer et tester l'authentification unique Azure AD avec Moxtra avec un utilisateur de test appelé « Britta Simon ».

Pour que l'authentification unique fonctionne, Azure AD doit savoir qui est l'utilisateur Moxtra équivalent dans Azure AD. En d'autres termes, il faut établir une relation entre l'utilisateur Azure AD et l'utilisateur Moxtra associé.<br> Pour cela, affectez la valeur du **nom d'utilisateur** dans Azure AD comme valeur du **nom d'utilisateur** dans Moxtra.
 
Pour configurer et tester l'authentification unique Azure AD avec Moxtra, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d'un utilisateur de test Moxtra](#creating-a-moxtra-test-user)** pour avoir un équivalent de Britta Simon dans Moxtra lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L'objectif de cette section est d'activer l'authentification unique Azure AD dans le portail Azure AD et de configurer l'authentification unique dans votre application Moxtra.

Votre application Moxtra attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d'attributs personnalisés à la configuration des attributs du jeton SAML. La capture d'écran suivante en montre un exemple. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) <br>



**Pour configurer l'authentification unique Azure AD avec Moxtra, procédez comme suit :**

1. Dans le portail Azure AD, sur la page d'intégration d'applications **Moxtra**, cliquez sur **Configurer l'authentification unique** pour ouvrir la boîte de dialogue **Configurer l'authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Moxtra**, sélectionnez **Authentification unique Azure AD**, puis sur **Suivant**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) <br>

3. Sur la page **Configurer les paramètres d'application**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) <br>

    a. Dans la zone de texte **URL d'authentification**, tapez l'URL suivante : ****https://www.moxtra.com/service/#login**.

    b. Cliquez sur **Next**.
 
 
4. Sur la page **Configurer l'authentification unique sur Moxtra**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d'entreprise Moxtra en tant qu'administrateur.

1. Dans la barre d'outils située à gauche, cliquez sur **Console Administrateur > Authentification unique SAML**, puis sur **Nouveau**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. Sur la page **SAML**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) <br>

    a. Dans la zone de texte **Nom**, tapez le nom de votre configuration (ex. *SAML*).

    b. Dans le portail Azure, sur la page **Configurer l'authentification unique sur Moxtra** de la boîte de dialogue, copiez la valeur de l'**ID d'entité**, puis collez-la dans la zone de texte **ID d'entité IdP**.

    c. Sur la page **Configurer l'authentification unique sur Moxtra** du portail Azure, copiez la valeur de l'**URL de connexion à distance**, puis collez-la dans la zone de texte **URL de connexion**.

    d. Dans la zone de texte **AuthnContextClassRef**, tapez **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    e. Dans le portail Azure, sur la page **Configurer l'authentification unique sur Moxtra**, copiez la valeur du **Format de l'identification du nom**, puis collez-la dans la zone de texte **Format du NameID**.

    f. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat**.

    g. Dans la zone de texte du domaine de messagerie SAML, tapez votre domaine de messagerie SAML.
    > [AZURE.NOTE]Pour connaître les étapes de vérification du domaine, cliquez sur le «**i**» ci-dessous.


    h. Cliquez sur **Mettre à jour**.


6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l'authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. À la page **Confirmation de l'authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][11]

1. Pour ajouter des mappages d'attributs personnalisés à votre configuration d'attributs de jeton SAML, cliquez sur **Attributs** dans le menu en haut pour ouvrir la boîte de dialogue **Attributs du jeton SAML**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. Pour chaque ligne de données dans le tableau ci-dessous, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | ---            | ---             |
    | firstname | givenname |
    | lastname | surname |
    | idpid | *< la valeur **ID d'entité** de la boîte de dialogue **Configurer l'authentification unique sur Moxtra** dans le portail Azure >* |

 
    a. Cliquez sur Ajouter un attribut utilisateur <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b. Dans la boîte de dialogue **Ajouter un attribut utilisateur**, tapez le nom et la valeur de l'attribut indiqués pour cette ligne dans le tableau. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c. Cliquez sur **Terminé**.



1. Cliquez sur **Appliquer les modifications**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) <br>








### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation **gauche du portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png) <br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. Dans la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, saisissez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a. Dans la zone de texte **Prénom**, tapez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, saisissez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. Dans la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d'un utilisateur de test Moxtra

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Moxtra.

**Pour créer un utilisateur appelé Britta Simon dans Moxtra, procédez comme suit :**

1. Connectez-vous à votre site d'entreprise Moxtra en tant qu'administrateur.

1. Dans la barre d'outils située à gauche, cliquez sur **Console Administrateur > Gestion des utilisateurs**, puis sur **Ajouter un utilisateur**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. Dans la boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :

    a. Dans la zone de texte **Prénom**, tapez **Britta**.

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **E-mail**, tapez l'adresse de messagerie de Britta dans le portail Azure.

    d. Dans la zone de texte **Division**, tapez **Développement**.

    e. Dans la zone de texte **Service**, tapez **Informatique**.

    f. Sélectionnez **Administrateur**.

    g. Cliquez sur **Add**.





### Affectation de l’utilisateur de test Azure AD

L'objectif de cette section est de permettre à Britta Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Moxtra. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à Moxtra, procédez comme suit :**

1. Dans la vue d’annuaire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **Moxtra**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du panneau d’accès.<br> Lorsque vous cliquez sur la vignette Moxtra dans le volet d'accès, vous êtes connecté automatiquement à votre application Moxtra.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO3-->