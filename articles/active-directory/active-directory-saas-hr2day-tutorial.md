---
title: 'Didacticiel : Intégration d’Azure Active Directory avec HR2day by Merces | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et HR2day by Merces.
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
ms.date: 09/01/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory avec HR2day by Merces
L’objectif de ce didacticiel est de vous montrer comment intégrer HR2day dans Azure Active Directory (Azure AD). L’intégration de HR2day dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à HR2day by Merces.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à HR2day by Merces (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis
Pour configurer l’intégration d’Azure AD avec HR2day by Merces, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement HR2day by Merces pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de HR2day by Merces à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## Ajout de HR2day by Merces à partir de la galerie
Pour configurer l’intégration de HR2day by Merces avec Azure AD, vous devez ajouter HR2day by Merces à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HR2day by Merces à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **HR2day by Merces**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)
7. Dans le volet de résultats, sélectionnez **HR2day by Merces**, puis cliquez sur **Terminer** pour ajouter l’application.

## Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec HR2day by Merces avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HR2day by Merces équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur HR2day associé doit être établie. Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans HR2day.

Pour configurer et tester l’authentification unique Azure AD avec HR2day by Merces, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test HR2day](#creating-a-hr2day-by-merces-test-user)** : pour avoir un équivalent de Britta Simon dans HR2day lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD
Cette section explique comment permettre aux utilisateurs de s’authentifier sur HR2day by Merces avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

Votre application HR2day by Merces attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)

Avant de pouvoir configurer votre assertion SAML, vous devez contacter l’équipe du support technique HR2day via l’adresse [servicedesk@merces.nl](mailto:servicedesk@merces.nl) et leur demander d’affecter la valeur d’attribut d’identificateur unique à votre client. Vous avez besoin de cette valeur pour exécuter les étapes de la section suivante.

**Pour configurer l’authentification unique Azure AD avec HR2day by Merces, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **HR2day by Merces**, dans le menu en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png)
2. Pour ajouter les mappages d’attribut requis, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png)

    a. Cliquez sur **Ajouter un attribut utilisateur**.

    b. Dans la zone de texte **Nom d’attribut**, saisissez **ATTR\_LOGINCLAIM**.

    c. Dans la liste **Valeur d’attribut**, sélectionnez **Join()**.

    d. Dans la liste **String1**, sélectionnez **User.mail**.

    e. Dans la zone de texte **String2**, saisissez **l’identificateur unique** fourni par votre équipe HR2day.

    f. Dans la zone de texte **Séparateur**, saisissez **@**.

    g. Cliquez sur **Terminé**.


1. Cliquez sur **Appliquer les modifications**.
2. Dans le menu du haut, saisissez **Démarrage rapide** afin d’ouvrir la boîte de dialogue **Démarrage rapide**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png)
3. Cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
4. Dans la page **Comment voulez-vous que les utilisateurs se connectent à HR2day**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png)
5. Sur la page **Configurer les paramètres d’application**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png)

    a. Dans la zone de texte URL d’authentification, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application HR2day by Merces, au format suivant : **« https://<nom du client>.force.com/<nom de l’instance> »**.

    b. Cliquez sur **Next**.

1. Dans la page **Configurer l’authentification unique sur HR2day by Merces**, procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Suivant**.
2. Pour configurer l’authentification unique pour votre application, contactez votre équipe du support technique HR2day by Merces à l’adresse [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) et joignez le fichier de certificat téléchargé à votre courrier électronique. Indiquez également l’URL d’authentification unique SAML, l’URL de déconnexion et l’URL de l’émetteur dans le cadre de l’intégration de l’authentification unique.

> [!NOTE]
> N’oubliez pas d’indiquer à l’équipe de Merces que cette intégration requiert la définition de l’ID d’entité en suivant ce modèle **https://hr2day.force.com/INSTANCENAME**
> 
> 

1. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
2. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page **Profil utilisateur**, procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **Prénom**, entrez **Britta**.
   
   b. Dans la zone de texte **Nom**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.

### Création d’un utilisateur test HR2day by Merces
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans HR2day by Merces. Collaborez avec l’équipe du support technique HR2day by Merces pour ajouter des utilisateurs dans le compte HR2day.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe de support HR2day by Merces.
> 
> 

### Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à HR2day by Merces.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à HR2day by Merces, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **HR2day by Merces**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Lorsque vous cliquez sur la vignette HR2day by Merces dans le volet d’accès, vous devez être connecté automatiquement à votre application HR2day by Merces.

## Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->