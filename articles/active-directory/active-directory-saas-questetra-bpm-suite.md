<properties
	pageTitle="Didacticiel : Intégration d'Azure Active Directory avec Questetra BPM Suite | azure.microsoft.com/ Aure"
	description="Découvrez comment configurer l'authentification unique entre Azure Active Directory et Questetra BPM Suite."
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
	ms.date="08/12/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d'Azure Active Directory avec Questetra BPM Suite

L’objectif de ce didacticiel est de vous montrer comment intégrer Questetra BPM Suite à Azure Active Directory (Azure AD). L’intégration de Questetra BPM Suite à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Questetra BPM Suite.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Questetra BPM Suite (via l'authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l'intégration d'Azure AD avec Questetra BPM Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.azure.microsoft.com/.com/pricing/free-trial/).

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Questetra BPM Suite à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Questetra BPM Suite à partir de la galerie
Pour configurer l'intégration de Questetra BPM Suite avec Azure AD, vous devez ajouter Questetra BPM Suite, disponible dans la galerie, à votre liste d'applications SaaS gérées.

**Pour ajouter Questetra BPM Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Questetra BPM Suite**.

	![Applications][5]

7. Dans le volet de résultats, sélectionnez **Questetra BPM Suite**, puis cliquez sur **Terminer** pour ajouter l’application.



##  Configuration et test de l’authentification unique Azure AD
L'objectif de cette section est de vous montrer comment configurer et tester l'authentification unique Azure AD avec Questetra BPM Suite avec un utilisateur de test appelé « Britta Simon ».

Pour que l'authentification unique fonctionne, Azure AD a besoin de savoir qui est l'utilisateur Questetra BPM Suite équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Questetra BPM Suite associé doit être établi. Pour cela, affectez la valeur de **nom d'utilisateur** dans Azure AD en tant que valeur du **nom d'utilisateur** dans Questetra BPM Suite.
 
Pour configurer et tester l'authentification unique Azure AD avec Questetra BPM Suite, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d'un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
4. **[Création d'un utilisateur de test Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)** pour avoir un équivalent de Britta Simon dans Questetra BPM Suite qui soit lié à la représentation Azure AD associée.
5. **[Affectation d'un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Questetra BPM Suite.

**Pour configurer l'authentification unique Azure AD avec Questetra BPM Suite, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Questetra BPM Suite**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][8]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Questetra BPM Suite**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][9]


3. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d'entreprise **Questetra BPM Suite** en tant qu'administrateur.

4. Dans le menu situé en haut, cliquez sur **Paramètres système**.

	![Authentification unique Azure AD][10]

5. Pour ouvrir la page **SingleSignOnSAML**, cliquez sur **SSO (SAML)**.

	![Authentification unique Azure AD][11]


6. Dans la page **Configurer les paramètres de l’application** du portail Azure Classic, procédez comme suit :

	![Configurer les paramètres d’application][13]
 
    a. Sur votre site d'entreprise **Questetra BPM Suite**, dans la section des informations SP, copiez l'**URL ACS** et collez-la dans la zone de texte **URL d'authentification**.

    b. Sur votre site d'entreprise **Questetra BPM Suite**, dans la section des informations SP, copiez l'**ID d'entité** et collez-le dans la zone de texte **URL de l'émetteur**.

    c. Sur votre site d'entreprise **Questetra BPM Suite**, dans la section des informations SP, copiez l'**URL ACS** et collez-la dans la zone de texte **URL de réponse**.

    d. Cliquez sur **Next**.

 
7. Dans la page **Configurer l’authentification unique à Questetra BPM Suite**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sur votre ordinateur.

	![Configurer l’authentification unique][14]


8. Sur votre site d’entreprise **Questetra BPM Suite**, procédez comme suit :

	![Configurer l’authentification unique][15]

    a. Sélectionnez **Activer l'authentification unique**.
     
    b. Dans le portail Azure Classic, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **ID d’entité**.

    c. Dans le portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL de page de connexion**.

    d. Dans le portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de page de déconnexion**.

    e. Dans la zone de texte **Format NameID**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.


    f. Créez un fichier encodé en base 64 à partir du certificat téléchargé.

    >[AZURE.TIP] Pour plus d'informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    g. Ouvrez votre certificat encodé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat de validation**.

    h. Cliquez sur **Save**.


9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Qu’est-ce qu’Azure AD Connect ?][17]


10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Qu’est-ce qu’Azure AD Connect ?][18]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Créer un utilisateur de test Azure AD][100]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Créer un utilisateur de test Azure AD][101]

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Créer un utilisateur de test Azure AD][102]

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Créer un utilisateur de test Azure AD][103]
 
    a. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.
  
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur Suivant.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Créer un utilisateur de test Azure AD][104]
  
    a. Dans la zone de texte **Prénom**, entrez **Britta**.
 
    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Créer un utilisateur de test Azure AD][105]

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Créer un utilisateur de test Azure AD][106]
  
    a. Notez la valeur du **Nouveau mot de passe**.
  
    b. Cliquez sur **Terminé**.
  
 
### Création d'un utilisateur de test Questetra BPM Suite

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Questetra BPM Suite.

**Pour créer un utilisateur appelé Britta Simon dans Questetra BPM Suite, procédez comme suit :**

1.	Connectez-vous à votre site d'entreprise Questetra BPM Suite en tant qu'administrateur.
2.	Accédez à **Paramètres système > Liste des utilisateurs > Nouvel utilisateur**.
3.	Dans la boîte de dialogue Nouvel utilisateur, procédez comme suit :

	![Créer un utilisateur de test][300]

    a. Dans la zone de texte **Nom**, entrez le nom d'utilisateur de Britta dans Azure AD.

    b. Dans la zone de texte **Adresse de messagerie**, entrez le nom d'utilisateur de Britta dans Azure AD.

    c. Dans la zone de texte **Mot de passe**, entrez un mot de passe.

4.	Cliquez sur **Ajouter un nouvel utilisateur**.



### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Questetra BPM Suite.

![Qu’est-ce qu’Azure AD Connect ?][200]

**Pour attribuer Britta Simon à Questetra BPM Suite, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

	![Qu’est-ce qu’Azure AD Connect ?][201]

2. Dans la liste des applications, sélectionnez **Questetra BPM Suite**.

	![Qu’est-ce qu’Azure AD Connect ?][205]

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Qu’est-ce qu’Azure AD Connect ?][202]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

	![Qu’est-ce qu’Azure AD Connect ?][203]

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Qu’est-ce qu’Azure AD Connect ?][204]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette Questetra BPM Suite dans le volet d'accès, vous devez être connecté automatiquement à votre application Questetra BPM Suite.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=AcomDC_0817_2016-->