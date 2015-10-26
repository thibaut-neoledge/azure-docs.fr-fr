<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Halogen Software"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Halogen Software."
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
	ms.date="10/01/2015"
	ms.author="markusvi"/>


# Didacticiel : Intégration d’Azure Active Directory avec Halogen Software

L’objectif de ce didacticiel est de vous montrer comment intégrer Halogen Software avec Azure Active Directory (Azure AD).<br>L’intégration de Halogen Software avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Halogen Software. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Halogen Software (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec Halogen Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Halogen Software pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Halogen Software à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Halogen Software à partir de la galerie
Pour configurer l’intégration de Halogen Software avec Azure AD, vous devez ajouter Halogen Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Halogen Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]
6. Dans la zone Rechercher, entrez **halogen software**.<br> ![Applications][5]
7. Dans le volet des résultats, sélectionnez **Halogen Software**, puis cliquez sur **Terminer** pour ajouter l’application.<br>



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Halogen Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Halogen Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Halogen Software associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Halogen Software.
 
Pour configurer et tester l’authentification unique Azure AD avec Halogen Software, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Halogen Software](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans Halogen Software qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Halogen Software.<br>

**Pour configurer l’authentification unique Azure AD avec Halogen Software, procédez comme suit :**

1. Dans le portail Azure AD, dans la page d’intégration d’applications **Halogen Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.<br><br>![Configurer l’authentification unique][8]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Halogen Software**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.<br><br> ![Authentification unique Azure AD][9]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer les paramètres d’application][10]
 
     3\.1 Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Halogen Software à l’aide du modèle suivant : **https://global.hgncloud.com/fabrikam/welcome.jsp*

     3\.2. Cliquez sur **Next**.
 
4. Sur la page **Configurer l’authentification unique à Halogen Software**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.<br><br>![Qu’est-ce qu’Azure AD Connect ?][11]

5. Dans une autre fenêtre de navigateur, connectez-vous à votre application **Halogen Software** en tant qu’administrateur.
6. Cliquez sur l’onglet **Options**.<br><br>![Qu’est-ce qu’Azure AD Connect ?][12]
7. Dans le volet de navigation de gauche, cliquez sur **SAML Configuration**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][13]
8. Sur la page **SAML Configuration**, procédez comme suit : <br><br>![Qu’est-ce qu’Azure AD Connect ?][14]

     8\.1. Dans **Unique Identifier**, sélectionnez **NameID**.

     8\.2. Dans **Unique Identifier Maps To**, sélectionnez **Username**.

     8\.3. Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Browse** pour sélectionner le fichier, puis sur **Upload File**.

     8\.4. Pour tester la configuration, cliquez sur **Run Test**. >[AZURE.NOTE]Vous devez attendre que le message « *The SAML test is complete. Please close this window* » s’affiche. Ensuite, fermez la fenêtre du navigateur. <br> La case à cocher **Enable SAML** est sélectionnée uniquement si le test a été effectué.

     8\.5. Sélectionnez **Enable SAML**.
    
     8\.6. Cliquez sur **Save Changes**.


9. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][15]
10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][16]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][100] 
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][101] 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][102] 
5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br>![Qu’est-ce qu’Azure AD Connect ?][103] 
  1. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.
  2. Dans la zone de texte **Nom d’utilisateur**, tapez **BrittaSimon**.
  3. Cliquez sur Suivant.
6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Qu’est-ce qu’Azure AD Connect ?][104] 
  1. Dans la zone de texte **Prénom**, tapez **Britta**.  
  2. Dans la zone de texte **Nom**, tapez **Simon**.
  3. Dans la zone de texte **Nom complet**, tapez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][105]  
8. Sur la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Qu’est-ce qu’Azure AD Connect ?][106]   
  1. Notez la valeur de **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   
  
 
### Création d’un utilisateur de test Halogen Software

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Halogen Software.

**Pour créer un utilisateur appelé Britta Simon dans Halogen Software, procédez comme suit :**

1. Connectez-vous à votre application **Halogen Software** en tant qu’administrateur.
2. Cliquez sur l’onglet **User Center**, puis sur **Create User**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][300]  
3. Sur la page **New User**, procédez comme suit : <br><br>![Qu’est-ce qu’Azure AD Connect ?][301]
  1. Dans la zone de texte **First Name**, tapez **Britta**. 
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Username**, tapez **Nom d’utilisateur de Britta Simon dans le portail Azure AD**.
  4. Dans la zone **Password**, entrez un mot de passe pour Britta.
  5. Cliquez sur **Save**.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Halogen Software.<br><br>![Qu’est-ce qu’Azure AD Connect ?][200]

**Pour attribuer Britta Simon à Halogen Software, procédez comme suit :**

1. Dans le portail Azure, pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br> <br><br>![Qu’est-ce qu’Azure AD Connect ?][201]
2. Dans la liste des applications, sélectionnez **Halogen Software**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][202]
1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.<br> <br><br>![Qu’est-ce qu’Azure AD Connect ?][203]
1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][204]
2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette Halogen Software dans le volet d’accès, vous devez être connecté automatiquement à votre application Halogen Software.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=Oct15_HO3-->