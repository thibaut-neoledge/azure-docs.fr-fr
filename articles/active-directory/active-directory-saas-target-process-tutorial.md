<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à TargetProcess | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et TargetProcess."
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


# Didacticiel : Intégration d’Azure AD à TargetProcess

L’objectif de ce didacticiel est de vous montrer comment intégrer TargetProcess à Azure Active Directory (Azure AD).

L’intégration de TargetProcess à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TargetProcess.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à TargetProcess (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec TargetProcess, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TargetProcess pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de TargetProcess à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de TargetProcess à partir de la galerie
Pour configurer l’intégration de TargetProcess avec Azure AD, vous devez ajouter TargetProcess à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter TargetProcess à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **TargetProcess**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_01.png)

7. Dans le volet de résultats, sélectionnez **TargetProcess**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_10.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec TargetProcess avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TargetProcess équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur TargetProcess associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans TargetProcess.
 
Pour configurer et tester l’authentification unique Azure AD avec TargetProcess, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d'un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test TargetProcess](#creating-a-targetprocess-test-user)** pour avoir un équivalent de Britta Simon dans TargetProcess lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application TargetProcess. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Pour configurer l’authentification unique pour TargetProcess, vous avez besoin d’un domaine enregistré. Si vous n’avez pas encore de domaine enregistré, contactez votre équipe de support TargetProcess via [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Pour configurer l’authentification unique Azure AD avec TargetProcess, procédez comme suit :**

1. Dans le portail Azure AD Classic, puis sur la page d’intégration d’applications **TargetProcess**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à TargetProcess**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_02.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_03.png)


    a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application TargetProcess (p. ex., *https://fabrikam.TargetProcess.com/*).

    b. Cliquez sur **Next**.
 
 
4. Sur la page **Configurer l’authentification unique sur TargetProcess**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_04.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


1. Connectez-vous à votre application TargetProcess en tant qu’administrateur.


1. Dans le menu situé en haut, cliquez sur **Setup**.

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

1. Cliquez sur **Paramètres**.

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png)

1. Cliquez sur **Single Sign-on**.

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png)

1. Dans la boîte de dialogue Paramètres d’authentification unique, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)

    a. Cliquez sur **Enable Single Sign-on**.

    b. Sur la page **Configurer l’authentification unique sur TargetProcess** du portail Azure Classic, copiez la valeur de l’**URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL de connexion**.

    c. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat**.

    d. Cliquez sur **Activer la configuration JIT**.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png)
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png)
 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png)
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test TargetProcess

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans TargetProcess. TargetProcess prend en charge l’approvisionnement juste-à-temps. Vous l’avez déjà activé dans [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

Vous n’avez aucune opération à effectuer dans cette section.




### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à TargetProcess.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à TargetProcess, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **TargetProcess**.

	![Configurer l’authentification unique](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_09.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette TargetProcess dans le volet d’accès, vous devez être connecté automatiquement à votre application TargetProcess.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->