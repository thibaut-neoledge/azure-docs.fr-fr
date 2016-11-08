---
title: 'Didacticiel : Intégration d’Azure Active Directory à Trello | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Trello.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-trello"></a>Didacticiel : Intégration d’Azure Active Directory avec Trello
Dans ce didacticiel, vous allez apprendre à intégrer Trello à Azure Active Directory (Azure AD).

L’intégration de Trello dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Trello
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Trello (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Trello, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement **Trello** pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Trello depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-trello-from-the-gallery"></a>Ajout de Trello depuis la galerie
Pour configurer l’intégration de Trello avec Azure AD, vous devez ajouter Trello, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Trello à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Trello**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)
7. Dans le volet des résultats, sélectionnez **Trello**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Trello avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Trello équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Trello associé doit être établie.
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Trello. Pour configurer et tester l’authentification unique Azure AD avec Trello, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Trello](#creating-a-the-funding-portal-test-user)** pour avoir un équivalent de Britta Simon dans Trello lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Trello.

L’application Trello s’attend à ce que les assertions SAML contiennent des attributs spécifiques. Configurez les attributs suivants pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attributs** de l’application. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png) 

**Pour configurer l’authentification unique Azure AD avec Trello, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Trello**, dans le menu situé en haut, cliquez sur **Attributs**.
   
    ![Configurer l’authentification unique][5]
2. Dans la boîte de dialogue **Attributs du jeton SAML** , pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :

      | Nom de l'attribut | Valeur de l’attribut |
      | --- | --- |    
      | User.Email | user.mail |
      | User.FirstName | user.givenname |
      | User.LastName | user.surname |

    a. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)

    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur de l’attribut** , sélectionnez la valeur pour cette ligne.

    d. Cliquez sur **Terminé**. Puis, cliquez sur **Enregistrer les modifications** en bas de la page.

1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique][6]
2. Dans le portail Azure Classic, dans la page d’intégration d’applications **Trello**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][7] 
3. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Trello**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)
4. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)

    a. Dans la zone de texte URL de réponse, tapez une URL au format suivant : `https://trello.com/auth/saml/consume/<enterprise>`.

    b. Cliquez sur **Suivant**.

> [!NOTE]
> Vous devez obtenir le champ de données dynamiques **\<enterprise\>** de Trello. Si vous n’avez pas la valeur de ce champ, contactez l’équipe de support de Trello à l’adresse <mailto:support@trello.com> pour obtenir le champ de données dynamiques pour votre entreprise.
> 
> 

1. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)** et saisissez **l’URL de connexion** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)
   
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://trello.com/auth/saml/consume/<enterprise>`
   
    b. Cliquez sur **Suivant**
2. Dans la page **Configurer l’authentification unique sur Trello**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)
3. Pour obtenir l’authentification unique configurée pour votre application, accédez à la page [Configuration de l’authentification unique d’entreprise Trello](https://trello.com/sso-configuration) pour envoyer à l’équipe Trello l’URL de connexion et joignez le certificat téléchargé.
4. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
5. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Suivant**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-trello-test-user"></a>Création d’un utilisateur de test Trello
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Trello. Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Trello. Trello prend en charge l’approvisionnement juste à temps et un nouveau compte est créé la première fois que vous vous connectez à partir d’Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Trello.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Trello, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Trello**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Trello dans le volet d'accès, vous devez être connecté automatiquement à votre application Trello.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


