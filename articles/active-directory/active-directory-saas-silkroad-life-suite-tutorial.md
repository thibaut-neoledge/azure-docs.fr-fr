<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à SilkRoad Life Suite | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et SilkRoad Life Suite."
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
	ms.date="10/23/2015"
	ms.author="markusvi"/>


# Didacticiel : Intégration d’Azure Active Directory à SilkRoad Life Suite

L’objectif de ce didacticiel est de vous montrer comment intégrer SilkRoad Life Suite avec Azure Active Directory (Azure AD).<br>L’intégration de SilkRoad Life Suite avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SilkRoad Life Suite. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SilkRoad Life Suite (via l’authentification unique) avec leur compte Azure AD.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD avec SilkRoad Life Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SilkRoad Life Suite pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SilkRoad Life Suite à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de SilkRoad Life Suite à partir de la galerie
Pour configurer l’intégration de SilkRoad Life Suite dans Azure AD, vous devez ajouter SilkRoad Life Suite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SilkRoad Life Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone Rechercher, entrez **SilkRoad Life Suite**.<br><br> ![Applications][5]<br>
7. Dans le volet de résultats, sélectionnez **SilkRoad Life Suite**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>![Applications][50]<br>


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec SilkRoad Life Suite avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur SilkRoad Life Suite équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SilkRoad Life Suite associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD en tant que valeur du **nom d’utilisateur** dans SilkRoad Life Suite.
 
Pour configurer et tester l’authentification unique Azure AD avec SilkRoad Life Suite, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** pour avoir un équivalent de Britta Simon dans SilkRoad Life Suite qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application SilkRoad Life Suite.<br>

**Pour configurer l’authentification unique Azure AD avec SilkRoad Life Suite, procédez comme suit :**

5. Connectez-vous à votre site d’entreprise SilkRoad en tant qu’administrateur. 


    > [AZURE.NOTE]Pour obtenir l’accès à l’application d’authentification SilkRoad Life Suite pour configurer la fédération avec Microsoft Azure AD, contactez le support technique SilkRoad ou votre représentant SilkRoad Services.


6. Accédez à **Service Provider**, puis cliquez sur **Federation Details**. <br><br>![Authentification unique Azure AD][10] <br>


1. Cliquez sur **Download Federation Metadata**, puis enregistrez le fichier de métadonnées sur votre ordinateur. <br><br>![Authentification unique Azure AD][11] <br>

3. Dans le portail Azure AD, sur la page d’intégration des applications **SilkRoad Life Suite**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SilkRoad Life Suite**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.<br><br> ![Authentification unique Azure AD][7] <br>

3. Dans la page **Configurer les paramètres d’application** de la boîte de dialogue, procédez comme suit : <br><br>![Authentification unique Azure AD][8] <br>
 
    a. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre site SilkRoad Life Suite (par ex. : **https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).

    b. Ouvrez le fichier de métadonnées **Silkroad** téléchargé.

    c. Recherchez la balise **AssertionConsumerService**, puis copiez l’attribut **Location**. <br><br>![Authentification unique Azure AD][21] <br>
   
    d. Collez la valeur dans la zone de texte **URL de réponse**.
 
    e. Cliquez sur **Next**.
 
4. Dans la page **Configurer l’authentification unique sur SilkRoad Life Suite**, procédez comme suit : <br><br>![Authentification unique Azure AD][9] <br>

    a. Cliquez sur Télécharger le certificat, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.




1. Dans votre application **SilkRoad**, cliquez sur **Authentication Sources**. <br><br>![Authentification unique Azure AD][12] <br>



1. Cliquez sur **Add Authentication Source**. <br><br>![Authentification unique Azure AD][13] <br>



1. Dans la section **Add Authentication Source**, procédez comme suit : <br><br>![Authentification unique Azure AD][14] <br>

    a. Sous **Option 2 - Metadata File**, cliquez sur **Browse** pour charger le fichier de métadonnées que vous avez téléchargé.

    b. Cliquez sur **Create Identity Provider using File Data**.



1. Dans la section **Authentication Sources**, cliquez sur **Edit**. <br><br>![Authentification unique Azure AD][15] <br>


1. Dans la boîte de dialogue **Edit Authentication Source**, procédez comme suit : <br><br>![Authentification unique Azure AD][16] <br>

    a. Pour l’option **Enabled**, sélectionnez **Yes**.

    b. Dans la zone de texte **IdP Description**, entrez une description de votre configuration (par exemple : *Authentification unique Azure AD*).

    c. Dans la zone de texte **IdP Name**, entrez un nom spécifique de votre configuration (par exemple : *Azure SP*).

    d. Cliquez sur **Enregistrer**.


6. Désactivez toutes les autres sources d’authentification. <br><br>![Authentification unique Azure AD][17]<br>

7. Dans le portail Azure AD, dans la page **Confirmation de l’authentification unique**, cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][18]

1. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][19]


### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **Portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png) <br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) <br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) <br>

5. Dans la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Dans la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png) <br>
 
    a. Dans la zone de texte **Prénom**, tapez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, tapez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) <br>
 
8. Dans la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png) <br>
  
    a. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test SilkRoad Life Suite

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SilkRoad Life Suite. Britta doit disposer d’un identifiant d’authentification unique (parfois appelé *AuthParam*) correspondant à son **adresse e-mail** dans Azure AD.

**Pour créer un utilisateur appelé Britta Simon dans SilkRoad Life Suite, procédez comme suit :**

1. Demandez à votre équipe de support SilkRoad Life Suite de créer un utilisateur qui a comme attribut **SSO ID** l’**adresse e-mail** de Britta Simon dans Azure AD.



### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SilkRoad Life Suite. <br><br>![Affecter des utilisateurs][200] <br>

**Pour attribuer Britta Simon à SilkRoad Life Suite, procédez comme suit :**

1. Dans la vue répertoire du portail Azure, pour ouvrir la vue des applications, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>
2. Dans la liste des applications, sélectionnez **SilkRoad Life Suite**. <br><br>![Affecter des utilisateurs][202] <br>
1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.<br> <br>![Affecter des utilisateurs][203]<br>
1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du panneau d’accès.<br> Lorsque vous cliquez sur la vignette SilkRoad Life Suite dans le panneau d’accès, vous devez être connecté automatiquement à votre application SilkRoad Life Suite.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO1-->