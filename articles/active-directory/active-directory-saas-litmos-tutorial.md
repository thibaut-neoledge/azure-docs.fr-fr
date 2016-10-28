<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Litmos | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Litmos."
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
	ms.date="07/08/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec Litmos

L’objectif de ce didacticiel est de vous montrer comment intégrer Litmos dans Azure AD (Azure Active Directory). L’intégration de Litmos dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Litmos.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Litmos (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD avec Litmos, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Litmos pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Litmos à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Litmos à partir de la galerie
Pour configurer l’intégration de Litmos avec Azure AD, vous devez ajouter Litmos à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Litmos à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **Litmos**.

	![Applications][5]

7. Dans le volet de résultats, sélectionnez **Litmos**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Applications][500]


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Litmos avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Litmos équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Litmos associé doit être établie. Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Litmos.
 
Pour configurer et tester l’authentification unique Azure AD avec Litmos, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Litmos](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Litmos lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD Classic et de configurer l’authentification unique dans votre application Litmos. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Dans le cadre de la configuration, vous devez personnaliser les **Attributs du jeton SAML** pour votre application Litmos.

![Authentification unique Azure AD][17]

**Pour configurer l’authentification unique Azure AD avec Litmos, procédez comme suit :**

1. Dans la page d’intégration d’applications **Litmos** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Litmos**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
	![Authentification unique Azure AD][7]


1. Connectez-vous à votre site d’entreprise Litmos (p. ex., *https://azureapptest.litmos.com/account/Login*) en tant qu’administrateur.

	![Authentification unique Azure AD][21]


1. Dans la barre de navigation située à gauche, cliquez sur **Accounts**.

	![Authentification unique Azure AD][22]


1. Cliquez sur l’onglet **Integrations**.

	![Authentification unique Azure AD][23]


1. Sur l’onglet **Integrations**, accédez à **3rd Party Integrations**, puis cliquez sur l’onglet **SAML 2.0**.

	![Authentification unique Azure AD][24]

1. Copiez la valeur sous **The SAML endpoint for Litmos is:**.

	![Authentification unique Azure AD][26]


3. Dans la page **Configurer les paramètres de l’application** du portail Azure Classic, procédez comme suit :

	![Authentification unique Azure AD][8]
 
    a. Dans la zone de texte **Identificateur**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application Litmos (p. ex., *https://azureapptest.litmos.com/account/Login*).
     
    b. Dans la zone de texte **URL de réponse**, collez la valeur que vous avez copiée à partir de l’application Litmos à l’étape précédente.

    c. Cliquez sur **Next**.
 
4. Sur la page **Configurer l’authentification unique sur Litmos**, procédez comme suit :

	![Authentification unique Azure AD][2]

    a. Cliquez sur Télécharger le certificat, puis enregistrez le fichier sur votre ordinateur.


1. Dans votre application **Litmos**, procédez comme suit :

	![Authentification unique Azure AD][25]

    a. Cliquez sur **Enable SAML**.

    b. Créez un fichier **codé en base 64** à partir du certificat téléchargé.

    >[AZURE.TIP] Pour plus d’informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    c. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **SAML X.509 Certificate**.

    d. Cliquez sur **Enregistrer les modifications**.


6. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
  
	![Authentification unique Azure AD][11]


20. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

	![Configurer l’authentification unique][12]


24. Dans la boîte de dialogue **Ajouter un attribut utilisateur**, procédez comme suit :

	![Configurer l’authentification unique][14]

    | Nom de l'attribut | Valeur de l’attribut |
    | ---            | ---             |
    | Email | user.mail |
    | FirstName | user.givenname |
    | Lastname | user.surname |

    Pour chaque ligne de données dans le tableau ci-dessus, procédez comme suit :
   
    a. Cliquez sur **Ajouter un attribut utilisateur**.

	![Configurer l’authentification unique][15]


    a. Dans la zone de texte **Nom de l’attribut**, indiquez le **nom de l’attribut** affiché pour cette ligne.

    b. Sélectionnez la **Valeur de l’attribut** affichée pour cette ligne.

    c. Cliquez sur **Terminé** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur**.


25. Cliquez sur **Appliquer les modifications**.

	![Configurer l’authentification unique][16]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)

    a. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png)
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png)
 
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png)
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test Litmos

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Litmos. L’application Litmos prend en charge l’approvisionnement juste-à-temps. Cela signifie qu’un compte d’utilisateur est automatiquement créé si nécessaire pendant la tentative d’accès à l’application à l’aide du volet d’accès.

**Pour créer un utilisateur appelé Britta Simon dans Litmos, procédez comme suit :**


1. Connectez-vous à votre site d’entreprise Litmos (p. ex., *https://azureapptest.litmos.com/account/Login*) en tant qu’administrateur.

	![Authentification unique Azure AD][21]


1. Dans la barre de navigation située à gauche, cliquez sur **Accounts**.

	![Authentification unique Azure AD][22]


1. Cliquez sur l’onglet **Integrations**.

	![Authentification unique Azure AD][23]


1. Sur l’onglet **Integrations**, accédez à **3rd Party Integrations**, puis cliquez sur l’onglet **SAML 2.0**.

	![Authentification unique Azure AD][24]

1. Sélectionnez **Autogenerate Users:**.

	![Authentification unique Azure AD][27]


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Litmos.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Litmos, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Litmos**.

	![Affecter des utilisateurs][202]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette Litmos dans le volet d’accès, vous devez être connecté automatiquement à votre application Litmos.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png

<!---HONumber=AcomDC_0713_2016-->