<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec CS Stars | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et CS Stars."
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


# Didacticiel : Intégration d’Azure Active Directory avec CS Stars

L’objectif de ce didacticiel est de vous montrer comment intégrer CS Stars avec Azure Active Directory (Azure AD).<br>L’intégration de CS Stars avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à CS Stars. 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à CS Stars (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec CS Stars, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement CS Stars pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de CS Stars à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de CS Stars à partir de la galerie
Pour configurer l’intégration de CS Stars avec Azure AD, vous devez ajouter CS Stars disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter CS Stars à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut. <br><br>![Applications][2]<br>

4. Cliquez sur **Ajouter** en bas de la page. <br><br>![Applications][3]<br>

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**. <br><br>![Applications][4]<br>

6. Dans la zone de recherche, saisissez **CS Stars**. <br><br>![Applications][5]<br>

7. Dans le volet de résultats, sélectionnez **CS Stars**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>![Applications][400]<br>



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec CS Stars avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur CS Stars équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur CS Stars associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans CS Stars.
 
Pour configurer et tester l’authentification unique Azure AD avec CS Stars, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test CS Stars](#creating-a-cs-stars-test-user)** : pour avoir un équivalent de Britta Simon dans CS Stars lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** : permet de vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application CS Stars.<br>

**Pour configurer l’authentification unique Azure AD avec CS Stars, procédez comme suit :**

1. Dans le portail Azure AD, sur la page d’intégration d’applications **CS Stars**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**. <br><br>![Configurer l’authentification unique][6]<br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à CS Stars**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][7]<br>

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer les paramètres d’application][8]<br>
 
     3\.1. dans la zone de texte **URL d’authentification**, saisissez l’URL que vos utilisateurs utilisent pour se connecter à votre application CS Stars (par exemple : « **https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2*).

     >[AZURE.NOTE]Si vous ne connaissez pas la valeur exacte, contactez votre représentant Marsh ClearSight.

     3\.2. Cliquez sur **Next**.
 
4. Dans la page **Configurer l’authentification unique sur CS Stars**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur. <br><br>![Qu’est-ce qu’Azure AD Connect ?][9]<br>

5. Pour activer l’authentification unique pour CS Stars, contactez votre représentant Marsh ClearSigh et remettez le fichier de métadonnées.


6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][10]<br>

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][11]<br>




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.<br> Dans la liste des utilisateurs, sélectionnez **Britta Simon**. <br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, dans la barre d’outils au bas de la page, cliquez sur **Ajouter un utilisateur**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. Sur la page **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, saisissez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>
  1. Dans la zone de texte **Prénom**, saisissez **Britta**.  
  2. Dans la zone de texte **Nom**, saisissez **Simon**.
  3. Dans la zone de texte **Nom complet**, saisissez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. Sur la page **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>
  1. Notez la valeur **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

  
 
### Création d’un utilisateur de test CS Stars

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans CS Stars.

**Pour créer un utilisateur appelé Britta Simon dans CS Stars, procédez comme suit :**

1. Contactez votre représentant Marsh ClearSight.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à CS Stars. <br><br>![Affecter des utilisateurs][200]<br>

**Pour affecter Britta Simon à CS Stars, procédez comme suit :**

1. Dans le portail Azure, pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut. <br> <br>![Affecter des utilisateurs][201]<br>
2. Dans la liste des applications, sélectionnez **CS Stars**. <br><br>![Affecter des utilisateurs][202]<br>
1. Dans le menu situé en haut de l’écran, cliquez sur **Utilisateurs**.<br> <br><br>![Affecter des utilisateurs][203]<br>
1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située au bas de l’écran, cliquez sur **Affecter**. <br><br>![Affecter des utilisateurs][205]<br>



### Test de l’authentification unique

L’objectif de cette section consiste à tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette CS Stars dans le volet d’accès, vous devez être connecté automatiquement à votre application CS Stars.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png

<!---HONumber=Oct15_HO3-->