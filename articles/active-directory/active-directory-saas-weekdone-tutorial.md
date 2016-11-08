---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Weekdone | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Weekdone.
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
ms.date: 07/11/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory à Weekdone
L’objectif de ce didacticiel est de vous montrer comment intégrer Weekdone à Azure AD (Azure Active Directory).

L’intégration de Weekdone à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Weekdone.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Weekdone (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis
Pour configurer l’intégration d’Azure AD à Weekdone, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Weekdone pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Weekdone à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## Ajout de Weekdone à partir de la galerie
Pour configurer l’intégration de Weekdone à Azure AD, vous devez ajouter Weekdone à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Weekdone à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Weekdone**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_01.png)
7. Dans le volet de résultats, sélectionnez **Weekdone**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_02.png)

## Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Weekdone au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Weekdone équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Weekdone associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Weekdone.

Pour configurer et tester l’authentification unique Azure AD avec Weekdone, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Weekdone](#creating-a-weekdone-test-user)** pour avoir un équivalent de Britta Simon dans Weekdone lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Weekdone.

**Pour configurer l’authentification unique Azure AD avec Weekdone, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Weekdone**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Weekdone**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_04.png)

    a. Dans la zone de texte **URL de réponse**, tapez l’URL selon le modèle suivant : **« https://weekdone.com/a/azure »**.

    b. Dans la zone de texte **Identificateur**, tapez l’URL selon le modèle suivant : **« https://weekdone.com/a/azure/metadata »**.

    c. Cliquez sur **Next**.

1. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, sélectionnez **Affichez les paramètres avancés (facultatif)**, entrez l’**URL de connexion** et l’**Identificateur** et cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_06.png)
   
    a. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Weekdone, au format suivant : **« https://weekdone.com/a/azure »**.
   
    b. Dans la zone de texte **Identificateur**, tapez l’URL selon le modèle suivant : **« https://weekdone.com/a/azure/metadata »**.
   
    c. Cliquez sur **Next**.
2. Dans la page **Configurer l’authentification unique sur Weekdone**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_05.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le certificat sur votre ordinateur.
   
    b. Cliquez sur **Next**.
3. Pour que l’authentification unique soit configurée pour votre application, contactez l’équipe du support technique Weekdone à l’adresse hello@weekdone.com. Joignez le fichier de certificat téléchargé à votre courrier et partagez les URL des métadonnées (URL DE L’ÉMETTEUR, URL SSO SAML et URL DU SERVICE DE DÉCONNEXION UNIQUE) avec l’équipe Weekdone pour configurer l’authentification unique de son côté.
4. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
5. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_04.png)
5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **Prénom**, entrez **Britta**.
   
   b. Dans la zone de texte **Nom**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire** (Get temporary password), cliquez sur **Créer**. ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_07.png)
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.

### Création d’un utilisateur de test Weekdone
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Weekdone. Weekdone prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Weekdone s’il n’existe pas déjà. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique de Weekdone à l’adresse hello@weekdone.com.
> 
> 

### Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Weekdone.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Weekdone, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Weekdone**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Weekdone dans le volet d’accès, vous devez être connecté automatiquement à votre application Weekdone.

## Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->