<properties
	pageTitle="Didacticiel : Intégration d’ADP GlobalView à Azure Active Directory | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et ADP GlobalView."
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
	ms.date="06/17/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’ADP GlobalView à Azure Active Directory

L’objectif de ce didacticiel est de vous montrer comment intégrer ADP GlobalView à Azure Active Directory (Azure AD). L’intégration d’ADP GlobalView dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ADP GlobalView.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ADP GlobalView (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.


Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’ADP GlobalView à Azure AD, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ADP GlobalView pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’ADP GlobalView à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout d’ADP GlobalView à partir de la galerie
Pour configurer l’intégration d’ADP GlobalView à Azure AD, vous devez ajouter ADP GlobalView depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ADP GlobalView à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **ADP GlobalView**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)

7. Dans le volet de résultats, sélectionnez **ADP GlobalView**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec ADP GlobalView au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ADP GlobalView équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur ADP GlobalView associé. Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans ADP GlobalView.

Pour configurer et tester l’authentification unique Azure AD avec ADP GlobalView, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test ADP GlobalView](#creating-a-adp-globalview-test-user)** pour avoir un équivalent de Britta Simon dans ADP GlobalView lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application ADP GlobalView.

Votre application ADP GlobalView s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre un exemple : Le nom de la revendication sera toujours **« PersonImmutableID »** et sa valeur mappée à ExtensionAttribute2 qui contient la valeur EmployeeID de l’utilisateur. Ici, le mappage utilisateur d’Azure AD à ADP GlobalView est effectué avec EmployeeID, mais vous pouvez le mapper à une valeur différente également basée sur les paramètres de votre application. Collaborez avec l’équipe ADP GlobalView pour utiliser l’identificateur correct d’un utilisateur et mapper cette valeur avec la revendication **"PersonImmutableID"**. Vous pouvez également mapper les revendications Email et UserID, comme indiqué dans l’image ci-dessous.
 
![Configurer l’authentification unique](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png)

Avant de pouvoir configurer votre assertion SAML, vous devez contacter l’équipe du support ADP GlobalView et lui demander d’affecter la valeur d’attribut d’identificateur unique à votre client. Vous avez besoin de cette valeur pour configurer la revendication personnalisée pour votre application.


**Pour configurer l’authentification unique Azure AD avec ADP GlobalView, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **ADP GlobalView**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ADP GlobalView**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
	![Configurer l’authentification unique](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png)


    a. Dans la zone de texte **Identifiant**, tapez l’URL utilisée pour identifier l’application ADP GlobalView sous l’un des formats suivants : **https://<nom\_serveur>.globalview.adp.com/federate2** ou **https://<nom\_serveur>.globalview.adp.com/federate**


    b. Dans la zone de texte **URL de réponse**, tapez l’URL utilisée par Azure AD pour envoyer la réponse à l’application ADP GlobalView sous l’un des formats suivants : **https://<nom\_serveur>.globalview.adp.com/federate2/sp/ACS.saml2**, **https://<nom\_serveur>.globalview.adp.com/federate/sp/ACS.saml2**

    c. Cliquez sur **Next**.


4. Dans la page **Configurer l’authentification unique sur ADP GlobalView**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support ADP GlobalView et envoyez-lui les éléments suivants :

    - Le fichier de certificat téléchargé
    - L’ID d’entité
    - L’URL SSO SAML
    - L’URL du service de déconnexion unique


    > [AZURE.NOTE] Quand l’équipe **ADP GlobalView** a configuré l’instance, demandez-lui la valeur **RelayState** et demandez à Azure AD de configurer cela pour votre instance d’application. Vous pouvez envoyer cette valeur, votre ID de client Azure AD et l’ID d’application à l’équipe Azure AD à l’adresse [waadpartners@microsoft.com](mailTo:waadpartners@microsoft.com). Une fois cette configuration effectuée, vous pouvez tester l’intégration et son bon fonctionnement. Notez qu’il s’agit d’une étape de configuration importante pour l’intégration de cette application.

6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Authentification unique Azure AD][11]



### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test ADP GlobalView

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ADP GlobalView. Collaborez avec l’équipe du support technique ADP GlobalView pour ajouter les utilisateurs au compte ADP GlobalView.


> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique ADP GlobalView.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à ADP GlobalView.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à ADP GlobalView, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **ADP GlobalView**.

	![Configurer l’authentification unique](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Quand vous cliquez sur la vignette ADP GlobalView dans le volet d’accès, vous êtes connecté automatiquement à votre application ADP GlobalView.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0622_2016-->