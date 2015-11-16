<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec CloudPassage | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et CloudPassage."
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
	ms.date="10/30/2015"
	ms.author="markvi"/>


# Didacticiel : Intégration d’Azure Active Directory avec CloudPassage

L’objectif de ce didacticiel est de vous montrer comment intégrer CloudPassage avec Azure Active Directory (Azure AD).<br>L’intégration de CloudPassage avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à CloudPassage. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à CloudPassage (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec CloudPassage, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement CloudPassage pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement de test Azure AD, vous pouvez obtenir un abonnement d’essai Azure gratuit d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de CloudPassage à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de CloudPassage à partir de la galerie
Pour configurer l’intégration de CloudPassage avec Azure AD, vous devez ajouter CloudPassage, disponible dans la galerie, à votre liste d’applications SaaS gérées.

### Pour ajouter CloudPassage à partir de la galerie, procédez comme suit :

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu supérieur. <br><br> ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]
6. Dans la zone de recherche, tapez **CloudPassage**.<br><br> ![Applications][5]
7. Dans le volet de résultats, sélectionnez **CloudPassage**, puis cliquez sur **Terminer** pour ajouter l’application.<br><br> ![Applications][6]



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec CloudPassage avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur CloudPassage équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur CloudPassage associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans CloudPassage.
 
Pour configurer et tester l’authentification unique Azure AD avec CloudPassage, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test CloudPassage](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans CloudPassage qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application CloudPassage.<br> Votre application CloudPassage attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre un exemple. <br><br> ![Configurer l’authentification unique][21]

**Pour configurer l’authentification unique Azure AD avec CloudPassage, procédez comme suit :**

1. Dans le portail Azure AD, puis sur la page d’intégration d’applications **CloudPassage**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.<br><br> ![Configurer l’authentification unique][7]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à CloudPassage**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.<br><br> ![Configurer l’authentification unique][8]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer les paramètres d’application][9]
 
     3\.1. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application CloudPassage (par ex. : **https://portal.cloudpassage.com/saml/init/accountid*).

     3\.2. Dans la zone de texte URL de réponse, tapez votre URL AssertionConsumerService (par ex., **https://portal.cloudpassage.com/saml/consume/accountid*). <br> Vous pouvez obtenir la valeur de cet attribut en cliquant sur **SSO Setup documentation** dans la section **Single Sign-on Settings** de votre portail CloudPassage. <br><br>![Configurer l’authentification unique][10]

     3\.3. Cliquez sur **Next**.



4. Sur la page **Configurer l’authentification unique à CloudPassage**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur. <br><br>![Configurer l’authentification unique][11]

5. Dans une autre fenêtre de navigateur Web, connectez-vous au site de votre entreprise CloudPassage en tant qu’administrateur.

6. Dans le menu situé en haut, cliquez sur **Settings** puis sur **Site Administration**. <br><br> ![Configurer l’authentification unique][12]

7. Cliquez sur l’onglet **Authentication Settings**. <br><br> ![Configurer l’authentification unique][13]


8. Dans la section **Single Sign-On Settings**, procédez comme suit : <br><br> ![Configurer l’authentification unique][14]


     8\.1. Dans la page **Configurer l’authentification unique sur CloudPassage** du portail Azure, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **SAML issuer URL**.

     8\.2. Dans le portail Azure, sur la page **Configurer l’authentification unique sur CloudPassage**, copiez la valeur **Point de terminaison initié par le fournisseur du service**, puis collez-la dans la zone de texte **SAML endpoint URL**.

     8\.3. Sur la page **Configurer l’authentification unique sur CloudPassage** du portail Azure, copiez la valeur **URL de déconnexion**, puis collez-la dans la zone de texte **Logout landing page**.

     8\.4. Créez un fichier codé en **base 64** à partir du certificat téléchargé.
          
      >[AZURE.TIP]Pour plus d’informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

     8\.5. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **x 509 certificate**.

     8\.6. Cliquez sur **Enregistrer**.


9. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Next**.<br><br> ![Configurer l’authentification unique][15]


10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br> ![Configurer l’authentification unique][16]



11. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**. <br><br> ![Configurer l’authentification unique][17]

12. Pour ajouter les attributs utilisateur requis, pour chaque ligne dans le tableau ci-dessous, procédez comme suit : <br>

| Nom de l'attribut | Valeur de l’attribut |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. Dans la barre d’outils en bas de la page, cliquez sur **Appliquer les modifications**. <br><br> ![Configurer l’authentification unique][20]



### Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br><br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**.<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**.<br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, tapez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Display Name**, tapez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. Sur la page **Obtenir un mot de passe temporaire**, procédez comme suit :<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   


  
 
### Création d’un utilisateur de test CloudPassage

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans CloudPassage.

#### Pour créer un utilisateur appelé Britta Simon dans CloudPassage, procédez comme suit :

1.	Connectez-vous à votre site d’entreprise **CloudPassage** en tant qu’administrateur. 

2.	Dans la barre d’outils située en haut, cliquez sur **Paramètres**, puis sur **Administration du site**. <br>![Création d’un utilisateur de test CloudPassage][22]

3.	Cliquez sur l’onglet **Users**, puis sur **Add a user**. <br>![Création d’un utilisateur de test CloudPassage][23]
	
4.	Dans la section **Add New User**, procédez comme suit : <br>![Création d’un utilisateur de test CloudPassage][24]

     4\.1. Dans la zone de texte **First Name**, tapez Britta.

     4\.2. Dans la zone de texte **Last Name**, tapez Simon.

     4\.3. Dans les zones de texte **Username**, **Email** et **Retype Email**, entrez le nom d’utilisateur de Britta dans Azure AD.

     4\.4. En tant que **Type d’accès**, sélectionnez **Activer l’accès portail Halo**.

     4\.5. Cliquez sur **Add**.










### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à CloudPassage.<br><br>![Affecter des utilisateurs][30]

**Pour attribuer Britta Simon à CloudPassage, procédez comme suit :**

1. Dans la vue d’annuaire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu du haut.<br> <br><br>![Affecter des utilisateurs][26]
2. Dans la liste des applications, sélectionnez **CloudPassage**. <br><br>![Affecter des utilisateurs][27]
1. Dans le menu situé en haut, cliquez sur **Users**.<br> <br><br>![Affecter des utilisateurs][25]
1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][29]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du panneau d’accès.<br> Lorsque vous cliquez sur la vignette CloudPassage dans le volet d’accès, vous devez être connecté automatiquement à votre application CloudPassage.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=Nov15_HO2-->