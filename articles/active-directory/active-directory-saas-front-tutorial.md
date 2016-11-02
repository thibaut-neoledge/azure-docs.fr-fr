<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Front | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Front."
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
	ms.date="08/09/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec Front

L’objectif de ce didacticiel est de vous montrer comment intégrer Front dans Azure Active Directory (Azure AD).

L’intégration de Front dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Front
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Front (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD avec Front, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Front pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Front depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Front depuis la galerie
Pour configurer l’intégration de Front avec Azure AD, vous devez ajouter Front, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Front à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
	
	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
	
	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche , tapez **Front**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)

7. Dans le volet des résultats, sélectionnez **Front**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Sélection de l’application dans la galerie](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Front avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Front équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Front associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Front.

Pour configurer et tester l’authentification unique Azure AD avec Front, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d'un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Front](#creating-a-front-test-user)** pour avoir un équivalent de Britta Simon dans Front lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Front.

**Pour configurer l’authentification unique Azure AD avec Front, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Front**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Front**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
    
	![Configurer l’authentification unique](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :

    ![Configurer l’authentification unique](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)

	a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.frontapp.com`

    b. Dans la zone de texte **URL de réponse**, saisissez une URL au format suivant : `https://<company name>.frontapp.com/sso/saml/callback`

	c. Cliquez sur **Suivant**

4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, saisissez **l’URL de connexion** et cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)

	a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.frontapp.com`

	b. Cliquez sur **Suivant**

	> [AZURE.NOTE] Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, vous pouvez vous référer à l’**étape 12** pour plus d’informations ou contacter Front à l’adresse : [support@frontapp.com](emailTo:support@frontapp.com).

5. Dans la page **Configurer l’authentification unique sur Front**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.

6. Connectez-vous à votre client Front en tant qu’administrateur.

7. Accédez à **Paramètres (icône représentant une roue dentée en bas de la barre latérale gauche) > Préférences**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

8. Cliquez sur le lien **Authentification unique**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

9. Sélectionnez **SAML** dans la liste déroulante **Authentification unique**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

10. Dans la zone de texte **Point d’entrée** copiez la valeur de **URL du service d’authentification unique** indiquée dans l’Assistant Configuration de l’application Azure AD.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

11. Copiez le contenu du fichier de certificat téléchargé, puis collez-le dans la zone de texte **Certificat de signature**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

12. Vérifiez que ces URL correspondent à votre configuration à l’étape 3.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

13. Cliquez sur le bouton **Enregistrer**.

14. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
	![Authentification unique Azure AD][10]

15. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
    
	![Authentification unique Azure AD][11]



### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Front

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Front. Veuillez contacter l’équipe de support Front pour ajouter les utilisateurs dans le compte Front.

### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Front.
	
![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Front, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
    
	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Front**.
    
	![Configurer l’authentification unique](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
	![Affecter des utilisateurs][203]

1. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Lorsque vous cliquez sur la vignette Front dans le volet d’accès, vous devez être connecté automatiquement à votre application Front.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->