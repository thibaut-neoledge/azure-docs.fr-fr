<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SciQuest Spend Director"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et SciQuest Spend Director."
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
	ms.date="07/30/2015"
	ms.author="markusvi"/>


# Didacticiel : Intégration d’Azure Active Directory avec SciQuest Spend Director

L’objectif de ce didacticiel est de vous montrer comment intégrer SciQuest Spend Director avec Azure Active Directory (Azure AD).<br>L’intégration de SciQuest Spend Director avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SciQuest Spend Director. 
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SciQuest Spend Director (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l’intégration d’Azure AD avec SciQuest Spend Director, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SciQuest Spend Director pour lequel l’authentification unique est activée


> [AZURE.NOTE]Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SciQuest Spend Director à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de SciQuest Spend Director à partir de la galerie
Pour configurer l’intégration de SciQuest Spend Director avec Azure AD, vous devez ajouter SciQuest Spend Director, qui est disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SciQuest Spend Director à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]
6. Dans la zone de recherche, entrez **sciQuest spend director**.<br> ![Applications][5]
7. Dans le volet des résultats, sélectionnez **SciQuest Spend Director**, puis cliquez sur **Terminer** pour ajouter l’application.<br>



##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec SciQuest Spend Director avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur SciQuest Spend Director équivalent à un utilisateur dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SciQuest Spend Director associé doit être établi.<br> Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans SciQuest Spend Director.
 
Pour configurer et tester l’authentification unique Azure AD avec SciQuest Spend Director, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test SciQuest Spend Director](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans SciQuest Spend Director qui soit lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD et de configurer l’authentification unique dans votre application SciQuest Spend Director.<br>

**Pour configurer l’authentification unique Azure AD avec SciQuest Spend Director, procédez comme suit :**

1. Dans le portail Azure AD, dans la page d’intégration d’applications **SciQuest Spend Director**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.<br><br>![Configurer l’authentification unique][8]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à SciQuest Spend Director**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.<br><br> ![Authentification unique Azure AD][9]

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit : <br><br>![Configurer les paramètres d’application][10]
 
     3\.1. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SciQuest Spend Director à l’aide du modèle suivant : **https://.*sciquest.com/.**

     3\.2. Dans la zone de texte **URL de réponse**, tapez la valeur que vous venez de taper dans la zone de texte **URL d’authentification**.

     3\.3. Cliquez sur **Next**.
 
4. Sur la page **Configurer l’authentification unique à SciQuest Spend Director**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.<br><br>![Qu’est-ce qu’Azure AD Connect ?][11]

5. Contactez le support technique de SciQuest pour activer cette méthode d’authentification à l’aide des métadonnées téléchargées ci-dessus.

6. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][15]
10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![What is Azure AD Connect][16]




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
  
 
### Création d’un utilisateur de test SciQuest Spend Director

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SciQuest Spend Director.

Vous devez contacter votre équipe de support SciQuest Spend Director et leur fournir les détails relatifs au compte que vous voulez créer.

Sinon, vous pouvez également tirer parti de l’approvisionnement juste-à-temps, une fonctionnalité d’authentification unique prise en charge par SciQuest Spend Director. <br> Si l’approvisionnement juste-à-temps est activé, les utilisateurs sont automatiquement créés par SciQuest Spend Director lors d’une tentative d’authentification unique s’ils n’existent pas. Cette fonctionnalité élimine le besoin de créer manuellement les utilisateurs équivalents de l’authentification unique.

Pour que l’approvisionnement juste-à-temps soit activé, vous devez contacter votre équipe de support SciQuest Spend Director.
  

### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SciQuest Spend Director.<br><br>![Qu’est-ce qu’Azure AD Connect ?][200]

**Pour affecter Britta Simon à SciQuest Spend Director, procédez comme suit :**

1. Dans le portail Azure, pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.<br> <br><br>![Qu’est-ce qu’Azure AD Connect ?][201]
2. Dans la liste des applications, sélectionnez **SciQuest Spend Director**. <br><br>
1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.<br> <br><br>![Qu’est-ce qu’Azure AD Connect ?][202]
1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][203]
2. Dans la barre d’outils située en bas, cliquez sur **Affecter**. <br><br>![Qu’est-ce qu’Azure AD Connect ?][204]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la vignette SciQuest Spend Director dans le volet d’accès, vous devez être connecté automatiquement à votre application SciQuest Spend Director.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!---HONumber=August15_HO6-->