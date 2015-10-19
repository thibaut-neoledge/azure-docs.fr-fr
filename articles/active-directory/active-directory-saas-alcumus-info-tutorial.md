<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Alcumus Info Exchange | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Alcumus Info Exchange."
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
	ms.date="10/06/2015"
	ms.author="markusvi"/>


# Didacticiel : Intégration d’Azure Active Directory avec Alcumus Info Exchange

L’objectif de ce didacticiel est de vous montrer comment intégrer Alcumus Info Exchange avec Azure Active Directory (Azure AD).<br>L’intégration d’Alcumus Info Exchange avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Alcumus Info Exchange. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Alcumus Info Exchange (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec Alcumus Info Exchange, vous avez besoin des éléments suivants :

- Un abonnement [Azure AD](http://azure.microsoft.com/)
- Un abonnement [Alcumus Info Exchange](http://www.alcumusgroup.com/) pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez obtenir une version d’essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Alcumus info Exchange à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout d’Alcumus info Exchange à partir de la galerie
Pour configurer l’intégration d’Alcumus Info Exchange dans Azure AD, vous devez ajouter Alcumus Info Exchange, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Alcumus Info Exchange à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut. <br><br>![Applications][2]<br>

4. Cliquez sur **Ajouter** en bas de la page. <br><br>![Applications][3]<br>

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**. <br><br>![Applications][4]<br>

6. Dans la zone Rechercher, saisissez **Alcumus informations Exchange**. <br><br>![Applications][5]<br>

7. Dans le volet des résultats, sélectionnez **Alcumus Info Exchange**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>![Applications][400]<br>



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Alcumus Info Exchange avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Alcumus Info Exchange équivalent à un utilisateur dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Alcumus Info Exchange associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Alcumus Info Exchange.
 
Pour configurer et tester l’authentification unique Azure AD avec Alcumus Info Exchange, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)** pour avoir un équivalent de Britta Simon dans Alcumus Info Exchange lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** : permet de vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application Alcumus Info Exchange.<br>

**Pour configurer l’authentification unique Azure AD avec Alcumus Info Exchange, procédez comme suit :**

1. Dans le portail Azure AD, puis sur la page d’intégration d’applications **Alcumus Info Exchange**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.<br><br> ![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Alcumus Info Exchange**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.<br><br> ![Authentification unique Azure AD][7]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Authentification unique Azure AD][8]<br>
 
     3\.1 dans la zone de texte **URL de réponse**, saisissez l’URL du consommateur installé pour vous par l’équipe du support technique Exchange Info Alcumus.

     >[AZURE.NOTE]Si vous ne connaissez pas la valeur adéquate, contactez l’équipe du support technique Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

     3\.2. Cliquez sur **Next**.
 
4. Sur la page **Configurer l’authentification unique à Alcumus Info Exchange**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.<br><br>![Qu’est-ce qu’Azure AD Connect ?][9]

5. Contactez l’équipe de support Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), fournissez-leur un fichier de métadonnées et faites-leur savoir qu’ils doivent activer SSO pour vous.


6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail de gestion Azure**, cliquez sur **Active Directory**.<br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png)
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png)

5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png)
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, saisissez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png)
  1. Dans la zone de texte **Prénom**, saisissez **Britta**.  
  2. Dans la zone de texte **Nom**, saisissez **Simon**.
  3. Dans la zone de texte **Nom complet**, saisissez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.<br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)
 
8. Sur la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
  1. Notez la valeur **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

  
 
### Création d’un utilisateur de test Alcumus informations Exchange

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Alcumus Info Exchange.

**Pour créer un utilisateur appelé Britta Simon dans Alcumus Info Exchange, procédez comme suit :**

1. Contactez l’équipe de support technique Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Alcumus Info Exchange.<br><br>![Affecter des utilisateurs][200]

**Pour attribuer Britta Simon à Alcumus Info Exchange, procédez comme suit :**

1. Dans le portail Azure, pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br> <br><br>![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Alcumus Info Exchange **. <br><br>![Affecter des utilisateurs][202]
1. Dans le menu situé en haut de l’écran, cliquez sur **Utilisateurs**.<br> <br><br>![Affecter des utilisateurs][203]
1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située au bas de l’écran, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section consiste à tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette Alcumus Info Exchange dans le volet d’accès, vous devez vous connecter automatiquement à votre application Alcumus Info Exchange.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

<!---HONumber=Oct15_HO2-->