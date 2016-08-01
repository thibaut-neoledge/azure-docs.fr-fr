<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec StatusPage | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et StatusPage."
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
	ms.date="07/14/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à StatusPage

L’objectif de ce didacticiel est de vous montrer comment intégrer StatusPage dans Azure AD (Azure Active Directory).

L’intégration de StatusPage dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à StatusPage.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à StatusPage (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD avec StatusPage, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement StatusPage pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de StatusPage à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de StatusPage à partir de la galerie
Pour configurer l’intégration de StatusPage avec Azure AD, vous devez ajouter StatusPage à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter StatusPage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
 
	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **StatusPage**.
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)

7. Dans le volet des résultats, sélectionnez **StatusPage**, puis cliquez sur **Terminé** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec StatusPage au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur StatusPage équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur StatusPage associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans StatusPage.
 
Pour configurer et tester l’authentification unique Azure AD avec StatusPage, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test StatusPage](#creating-a-statuspage-test-user)** pour avoir un équivalent de Britta Simon dans StatusPage qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application StatusPage.



**Pour configurer l’authentification unique Azure AD avec StatusPage, procédez comme suit :**

1. Dans le portail Azure Classic, puis sur la page d’intégration d’application **StatusPage**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à StatusPage**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png)


3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :
 
	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png)

    > [AZURE.NOTE] Contactez l’équipe de support StatusPage à l’adresse [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) pour demander les métadonnées nécessaires à la configuration de l’authentification unique.


    a. À partir des métadonnées, copiez la valeur de l’émetteur et collez-la dans la zone de texte **Identificateur**.

    b. À partir des métadonnées, copiez l’URL de réponse et collez-la dans la zone de texte **URL de réponse**.

    c. Cliquez sur **Next**.
 
 
4. Sur la page **Configurer l’authentification unique sur StatusPage**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.

1. Dans la barre d’outils principale, cliquez sur **Manage Account** (Gérer le compte).

	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png)


1. Cliquez sur l’onglet **Single Sign-on** (Authentification unique).

	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png)


1. Sur la page de configuration de l’authentification unique, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png)

    a. Dans le portail Azure Classic, sur la page **Configurer l’authentification unique sur StatusPage**, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL cible d’authentification unique**.

    b. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat**.

    c. Cliquez sur **Enregistrer**.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
  
	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.



![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png)
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png)
 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png)
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test StatusPage

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans StatusPage. StatusPage prend en charge l’approvisionnement juste-à-temps. Vous l’avez déjà activé dans [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).


**Pour créer un utilisateur appelé Britta Simon dans StatusPage, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Manage Account**.

1. Cliquez sur l’onglet Team Members (Membres de l’équipe).

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png)

1. Cliquez sur l’onglet **Add Team Member** (Ajouter un membre d’équipe).

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png)

1. Entrez l’adresse électronique, le prénom et le nom de l’utilisateur valide que vous souhaitez configurer dans les zones de texte correspondantes, à savoir, **Email Address** (Adresse électronique), **First Name** (Prénom) et **Sur Name** (Nom).

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png)

1. Pour **Role**, choisissez **Client Administrator**.

1. Cliquez sur **Create Account**.

### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à StatusPage.

![Affecter des utilisateurs][200]

**Pour attribuer Britta Simon à StatusPage, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **StatusPage**.

	![Configurer l’authentification unique](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette StatusPage dans le volet d’accès, vous devez être connecté automatiquement à votre application StatusPage.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0720_2016-->