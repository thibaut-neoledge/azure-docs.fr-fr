<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Lesson.ly | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lesson.ly."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-lesson.ly"></a>Didacticiel : Intégration d’Azure Active Directory à Lesson.ly

L’objectif de ce didacticiel est de vous montrer comment intégrer Lesson.ly dans Azure AD (Azure Active Directory).

L’intégration de Lesson.ly dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Lesson.ly.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Lesson.ly (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Lesson.ly, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Lesson.ly pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Lesson.ly à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-lesson.ly-from-the-gallery"></a>Ajout de Lesson.ly à partir de la galerie
Pour configurer l’intégration de Lesson.ly à Azure AD, vous devez ajouter Lesson.ly de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Lesson.ly à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
 
    ![Applications][4]

6. Dans la zone de recherche, entrez **Lesson.ly**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)

7. Dans le volet de résultats, sélectionnez **Lesson.ly**, puis cliquez sur **Terminer** pour ajouter l’application.


![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique avec Azure AD avec Lesson.ly en fonction d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur de Lesson.ly équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur de Lesson.ly associé doit être établie.

Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Lesson.ly.

Pour configurer et tester l’authentification unique avec Azure AD avec Lesson.ly, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Lesson.ly](#creating-a-Lessonly-test-user)** pour avoir un équivalent de Britta Simon dans Lesson.ly lié à la représentation Azure AD associée.
5. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Lesson.ly avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

Votre application Lesson.ly s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML** .
La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) 

**Pour configurer l’authentification unique avec Azure AD avec Lesson.ly, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration de l’application Lesson.ly, dans le menu en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) 

2. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) 

    a. Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut utilisateur**.

    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur de l’attribut** , sélectionnez la valeur d’attribut pour cette ligne.

    d. Cliquez sur **Terminé**

3. Cliquez sur **Appliquer les modifications**.

4. Dans votre navigateur, cliquez sur **Précédent** pour rouvrir la boîte de dialogue Démarrage rapide

5. Cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique][6] 

6. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Lesson.ly ?**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) 

7. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
 
    ![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) 


    a. Dans la zone de texte URL de connexion, tapez l’URL dont vos utilisateurs se servent pour se connecter à votre application Lesson.ly au format suivant : **« https://nomentreprise.Lesson.ly/signin »**. Lors du référencement d’un nom générique, la partie **nomentreprise** doit être remplacée par un nom réel.


8. Sur la page **Configurer l’authentification unique sur Lesson.ly** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) 

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


9. Pour que l’authentification unique soit configurée pour votre application, contactez votre équipe du support technique Lesson.ly à l’adresse dev@lesson.ly. Joignez le fichier de certificat téléchargé à votre courrier et partagez les URL des métadonnées (ID d’entité, URL d’authentification unique et URL de déconnexion) avec l’équipe Lesson.ly pour configurer l’authentification unique de son côté.


10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Authentification unique Azure AD][10]

11. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
  
    ![Authentification unique Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-lesson.ly-test-user"></a>Création d’un utilisateur de test Lesson.ly

L’objectif de cette section est de créer l’utilisateur Britta Simon dans Lesson.ly. Lesson.ly. prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur sera créé lors d’une tentative d’accès à Lesson.ly s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Lesson.ly.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Lesson.ly.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Lesson.ly, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Lesson.ly**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) 

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Lesson.ly dans le volet d’accès, vous devez être connecté automatiquement à votre application Lesson.ly.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


