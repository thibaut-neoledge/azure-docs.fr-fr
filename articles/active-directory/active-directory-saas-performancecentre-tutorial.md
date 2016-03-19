<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à PerformanceCentre | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et PerformanceCentre."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à PerformanceCentre

L’objectif de ce didacticiel est de vous montrer comment intégrer PerformanceCentre avec Azure Active Directory (Azure AD).<br>L’intégration de PerformanceCentre avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à PerformanceCentre. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à PerformanceCentre (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise 

Pour configurer l’intégration d’Azure AD avec PerformanceCentre, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement PerformanceCentre pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de PerformanceCentre à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de PerformanceCentre à partir de la galerie
Pour configurer l’intégration de PerformanceCentre avec Azure AD, vous devez ajouter PerformanceCentre à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter PerformanceCentre à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, entrez **PerformanceCentre**.<br><br> ![Applications][5]<br>
7. Dans le volet des résultats, sélectionnez **PerformanceCentre**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>![Applications][500]<br>


##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec PerformanceCentre avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur PerformanceCentre équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur PerformanceCentre associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans PerformanceCentre.
 
Pour configurer et tester l’authentification unique Azure AD avec PerformanceCentre, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test PerformanceCentre](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans PerformanceCentre qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD Classic et de configurer l’authentification unique dans votre application PerformanceCentre.<br>

**Pour configurer l’authentification unique Azure AD avec PerformanceCentre, procédez comme suit :**

1. Dans la page d’intégration d’applications **PerformanceCentre** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à PerformanceCentre**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br> ![Authentification unique Azure AD][7] <br>

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Authentification unique Azure AD][8] <br>
 
     a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre site PerformanceCentre (p. ex., **http://companyname.performancecentre.com/saml/SSO*).
 
     b. Cliquez sur **Next**.
 
4. Sur la page **Configurer l’authentification unique sur PerformanceCentre**, procédez comme suit : <br><br>![Authentification unique Azure AD][9] <br>

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.



1. Connectez-vous à votre site d’entreprise **PerformanceCentre** en tant qu’administrateur.

2. Dans l’onglet sur le côté gauche, cliquez sur **Configure**. <br><br>![Authentification unique Azure AD][10]

2. Dans l’onglet sur le côté gauche, cliquez sur **Miscellaneous**, puis cliquez sur **Single Sign On**. <br><br>![Authentification unique Azure AD][11]

2. Pour **Protocol**, sélectionnez **SAML**. <br><br>![Authentification unique Azure AD][12]

2. Ouvrez votre fichier de métadonnées téléchargé dans le Bloc-notes, copiez son contenu, collez-le dans la zone de texte **Identity Provider Metadata**, puis cliquez sur **Save**. <br><br>![Authentification unique Azure AD][13]

2. Vérifiez que les valeurs **Entity Base URL** et **Entity ID URL** sont correctes. <br><br>![Authentification unique Azure AD][14]


6. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][15]<br>

7. Dans la page **Confirmation de l'authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][16]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d’un utilisateur de test PerformanceCentre

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans PerformanceCentre.

**Pour créer un utilisateur appelé Britta Simon dans PerformanceCentre, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise PerformanceCentre en tant qu’administrateur.

2. Dans le menu de gauche, cliquez sur **Interrelate**, puis cliquez sur **Create Participant**. <br><br>![Créer un utilisateur][400]<br>

4. Dans la boîte de dialogue **Interrelate - Create Participant**, effectuez les opérations suivantes : <br><br>![Créer un utilisateur][401]<br>

    a. Entrez les attributs requis pour Britta Simon dans les zones de texte correspondantes.
    
    > [AZURE.IMPORTANT] L’attribut de nom d’utilisateur de Britta dans PerformanceCentre doit être le même que le nom d’utilisateur dans Azure AD.


    b. Sélectionnez **Client Administrator** pour **Choose Role**.

    c. Cliquez sur **Enregistrer**.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à PerformanceCentre. <br><br>![Affecter des utilisateurs][200] <br>

**Pour attribuer Britta Simon à PerformanceCentre, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>
2. Dans la liste des applications, sélectionnez **PerformanceCentre**. <br><br>![Affecter des utilisateurs][202] <br>
1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>
1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette PerformanceCentre dans le volet d’accès, vous devez être connecté automatiquement à votre application PerformanceCentre.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png

<!---HONumber=AcomDC_0128_2016-->