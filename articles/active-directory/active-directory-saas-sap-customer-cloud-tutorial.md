<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SAP Cloud pour le client | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud pour le client."
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
	ms.date="06/20/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec SAP Cloud pour le client

Dans ce didacticiel, vous allez apprendre à intégrer SAP Cloud pour le client à Azure Active Directory (Azure AD).

L’intégration de SAP Cloud pour le client à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SAP Cloud pour le client.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SAP Cloud pour le client (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD avec SAP Cloud pour le client, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement avec authentification unique SAP Cloud pour le client


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP Cloud pour le client à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de SAP Cloud pour le client à partir de la galerie
Pour configurer l’intégration de SAP Cloud pour le client à Azure AD, vous devez ajouter SAP Cloud pour le client depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Cloud pour le client à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **SAP Cloud pour le client**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_01.png)

7. Dans le volet des résultats, sélectionnez **SAP Cloud pour le client**, puis cliquez sur **Terminé** pour ajouter l’application.

	![Active Directory](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_02.png)



##  Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Cloud pour le client, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Cloud pour le client équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SAP Cloud pour le client associé doit être établi.

Pour configurer et tester l’authentification unique Azure AD avec SAP Cloud pour le client, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SAP Cloud pour le client](#creating-an-sap-business-bydesign-test-user)** pour avoir un équivalent de Britta Simon dans SAP Cloud pour le client lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application SAP Cloud pour le client.


**Pour configurer l’authentification unique Azure AD avec SAP Cloud pour le client, procédez comme suit :**


1. Dans le portail Azure Classic, dans la page d’intégration d’application **SAP Cloud pour le client**, cliquez sur **Attributs** dans le menu situé en haut.

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_80.png)


2. Dans la liste des attributs du jeton SAML, sélectionnez l’attribut nameidentifier, puis cliquez sur **Modifier**.

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_84.png)

3. Dans la boîte de dialogue **Modifier un attribut utilisateur**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_85.png)


	a. À partir de la liste **Valeur d’attribut**, sélectionnez la fonction **ExtractMailPrefix()**.
	
	b. Dans la liste **Courrier**, sélectionnez l’attribut utilisateur que vous souhaitez utiliser pour votre implémentation. Par exemple, si vous souhaitez utiliser EmployeeID comme identificateur d’utilisateur unique et que vous avez stocké la valeur d’attribut dans ExtensionAttribute2, sélectionnez user.extensionattribute2.

	c. Cliquez sur **Terminé**.
	

4. Dans le portail Azure Classic, sur la page d’intégration d’application **SAP Cloud pour le client**, cliquez sur **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

5. Dans la page **How would you like users to sign on to SAP Cloud for Customer** (Comment voulez-vous que les utilisateurs se connectent à SAP Cloud pour le client), sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_03.png)

6. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_04.png)

    a. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SAP Cloud pour le client, au format suivant : `https://<server name>.crm.ondemand.com`
	
	b. Cliquez sur **Suivant**
 
7. Dans la page **Configure single sign-on at SAP Cloud for Customer** (Configurer l’authentification unique sur SAP Cloud pour le client), procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_05.png)

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


8. Pour configurer l’authentification unique, procédez comme suit :

	a. Connectez-vous au portail SAP Cloud pour le client avec des droits d’administrateur.

	b. Accédez à **Application and User Management Common Task** (Tâche courante d’application et de gestion des utilisateurs) et cliquez sur l’onglet **Fournisseur d’identité**.

	c. Cliquez sur **New Identity Provider** (Nouveau fournisseur d’identité) et sélectionnez le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure Classic. En important les métadonnées, le système charge automatiquement le certificat de signature ainsi que le certificat de chiffrement requis.

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_54.png)

	d. Azure AD nécessite l’URL Assertion Consumer Service dans la requête SAML ; cochez donc la case **Include Assertion Consumer Service URL** (Inclure l’URL Assertion Consumer Service).

	e. Cliquez sur **Activate Single Sign-On** (Activer l’authentification unique).

	f. Enregistrez vos modifications.

	g. Cliquez sur l’onglet **My System** (Mon système).

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_52.png)

	h. Copiez **l’URL SSO** et collez-la dans la zone de texte **Azure AD Sign On URL** (URL d’authentification Azure AD).

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_53.png)

	i. Précisez si l’employé peut choisir manuellement entre l’authentification à l’aide d’un ID d’utilisateur/mot de passe ou l’authentification unique en cliquant sur **Manual Identity Provider Selection** (Sélection manuelle du fournisseur d’identité).

	j. Dans la section **URL SSO**, spécifiez l’URL devant être utilisée par vos employés pour se connecter au système. Dans la liste **URL Sent to Employee** (URL envoyée à l’employé), vous pouvez choisir entre les options suivantes :
	
	**Non-SSO URL** (URL non SSO)
 
	Le système envoie uniquement l’URL du système normale à l’employé. L’employé ne peut pas se connecter à l’aide de l’authentification unique ; il doit donc utiliser un mot de passe ou un certificat.

	**URL SSO**

	Le système envoie uniquement l’URL SSO à l’employé. L’employé peut se connecter à l’aide de l’authentification unique. La demande d’authentification est redirigée via l’IdP.

	**Sélection automatique**
 
	Si l’authentification unique n’est pas activée, le système envoie l’URL du système normale à l’employé. Si l’authentification unique est activée, le système vérifie si l’employé possède un mot de passe. Le cas échéant, les URL SSO et les URL non SSO sont envoyées à l’employé. Toutefois, si l’employé ne possède aucun mot de passe, seule l’URL SSO est envoyée à l’employé.

	k. Enregistrez vos modifications.

9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
	
	![Authentification unique Azure AD][10]

10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
	![Authentification unique Azure AD][11]


### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Parlez-nous de cet utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_07.png)

8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Créer un utilisateur de test pour SAP Cloud pour le client

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SAP Cloud pour le client. Travaillez avec l’équipe du support technique SAP Cloud pour le client pour ajouter des utilisateurs à la plateforme SAP Cloud pour le client.

> [AZURE.NOTE] Assurez-vous que la valeur NameID correspond au champ de nom d’utilisateur dans la plateforme SAP Cloud pour le client.

### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Cloud pour le client.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à SAP Cloud pour le client, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Cloud SAP pour le client**.

	![Configurer l’authentification unique](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]


### Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SAP Cloud pour le client dans le volet d’accès, vous devez être connecté automatiquement à votre application SAP Cloud pour le client.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0622_2016-->