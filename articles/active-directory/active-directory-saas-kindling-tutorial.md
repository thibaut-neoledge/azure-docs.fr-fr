<properties
	pageTitle="Didacticiel : Intégration d'Azure Active Directory avec Kindling | Microsoft Azure"
	description="Découvrez comment configurer l'authentification unique entre Azure Active Directory et Kindling."
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
	ms.date="02/10/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d'Azure Active Directory avec Kindling

L'objectif de ce didacticiel est de vous montrer comment intégrer Kindling avec Azure Active Directory (Azure AD).<br>L'intégration de Kindling avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Kindling 
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Kindling (via l'authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic. 


Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis 

Pour configurer l'intégration d'Azure AD avec Kindling, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kindling pour lequel l'authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. <br> Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kindling à partir de la galerie 
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Kindling à partir de la galerie
Pour configurer l'intégration de Kindling à Azure AD, vous devez ajouter Kindling à votre liste d'applications SaaS gérées à partir de la galerie.

**Pour ajouter Kindling à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.<br><br> ![Applications][2]<br>
4. Cliquez sur **Ajouter** en bas de la page.<br><br> ![Applications][3]<br>
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.<br><br> ![Applications][4]<br>
6. Dans la zone de recherche, tapez **Kindling**.<br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_01.png)<br>
7. Dans le volet des résultats, sélectionnez **Kindling**, puis cliquez sur **Terminer** pour ajouter l'application. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_02.png)<br>

##  Configuration et test de l’authentification unique Azure AD
L'objectif de cette section est de vous montrer comment configurer et tester l'authentification unique Azure AD avec Kindling avec un utilisateur de test appelé « Britta Simon ».

Pour que l'authentification unique fonctionne, Azure AD doit savoir qui est l'utilisateur Kindling équivalent dans Azure AD. En d'autres termes, il faut établir une relation entre l'utilisateur Azure AD et l'utilisateur Kindling associé.<br> Pour cela, affectez la valeur du **nom d'utilisateur** d'Azure AD comme valeur du **nom d'utilisateur** dans Kindling.
 
Pour configurer et tester l'authentification unique Azure AD avec Kindling, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d'un utilisateur de test Kindling](#creating-a-kindling-test-user)** pour avoir un équivalent de Britta Simon dans Kindling lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L'objectif de cette section est d'activer l'authentification unique Azure AD dans le portail Azure Classic et de configurer l'authentification unique dans votre application Kindling. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Pour configurer l'authentification unique pour Kindling, vous avez besoin d'un domaine enregistré. Si vous n'avez pas encore de domaine enregistré, contactez votre équipe de support Kindling via [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Pour configurer l'authentification unique Azure AD avec Kindling, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d'intégration d'application **Kindling**, cliquez sur **Configurer l'authentification unique** pour ouvrir la boîte de dialogue **Configurer l'authentification unique**. <br><br> ![Configurer l’authentification unique][6] <br>

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Kindling**, sélectionnez **Authentification unique Azure AD**, puis sur **Suivant**. <br><br> ![Configurer l’authentification unique](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_03.png) <br>

3. Sur la page **Configurer les paramètres d'application**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_04.png) <br>


    a. Dans la zone de texte **URL d'authentification**, tapez l'URL utilisée par vos utilisateurs pour se connecter à votre application Kindling (ex. **https://fabrikam.Kindling.com/*).

    b. Contactez votre équipe de support Kindling pour connaître la valeur de l'**Émetteur** et de l'**URL de réponse**.
 
    c. Cliquez sur **Next**.
 
 
4. Sur la page **Configurer l'authentification unique sur Kindling**, procédez comme suit : <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_05.png) <br>

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Contactez votre équipe Kindling et demandez-lui de configurer l'authentification unique pour votre application. Vous devrez fournir le certificat téléchargé, la valeur de l'**URL de l'émetteur** correspondant à l'**ID d'entité** de Kindling, l'**URL du service d'authentification unique** correspondant à l'**URL d'authentification unique** de Kindling et l'**URL du service de déconnexion unique** correspondant à l'**URL du service de déconnexion unique** de Kindling à l'équipe de support.

    c. Cliquez sur **Next**.

6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. <br><br>![Authentification unique Azure AD][10]<br>

7. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.<br> Dans la liste Utilisateurs, sélectionnez **Britta Simon**.<br><br>![Créer un utilisateur Azure AD][20]<br>

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**. <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_09.png) <br> 

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_03.png) <br>
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_04.png) <br>

5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_05.png) <br>

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_06.png) <br>
 
    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**. e. Cliquez sur **Next**.

7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**. <br><br> ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_07.png) <br>
 
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : <br><br>![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_08.png) <br>
  
    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.

  
 
### Création d'un utilisateur de test Kindling

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Kindling. Kindling prend en charge l'approvisionnement juste-à-temps. Vous l'avez déjà activé dans [Configuration de l'authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

Vous n’avez aucune opération à effectuer dans cette section.




### Affectation de l’utilisateur de test Azure AD

L'objectif de cette section est de permettre à Britta Simon d'utiliser l'authentification unique Azure en lui accordant l'accès à Kindling. <br><br>![Affecter des utilisateurs][200] <br>

**Pour affecter Britta Simon à Kindling, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut. <br><br>![Affecter des utilisateurs][201] <br>

2. Dans la liste des applications, sélectionnez **Kindling**. <br><br>![Configurer l’authentification unique](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_50.png) <br>

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**. <br><br>![Affecter des utilisateurs][203] <br>

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**. <br><br>![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.<br> Lorsque vous cliquez sur la mosaïque Kindling dans le volet d'accès, vous êtes connecté automatiquement à votre application Kindling.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0211_2016-->