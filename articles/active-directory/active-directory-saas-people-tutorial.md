---
title: 'Didacticiel : Intégration d’Azure Active Directory à People | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et People.
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
# Didacticiel : Intégration d’Azure Active Directory à People
L’objectif de ce didacticiel est de vous montrer comment intégrer People à Azure AD (Azure Active Directory).

L’intégration de People à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à People
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à People (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Configuration requise
Pour configurer l’intégration d’Azure AD à People, vous avez besoin des éléments suivants :

* Un abonnement Azure
* Un abonnement People pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de People à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## Ajout de People à partir de la galerie
Pour configurer l’intégration de People à Azure AD, vous devez ajouter People, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter People à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **People**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_01.png)
7. Dans le volet de résultats, sélectionnez **People**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_02.png)

## Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec People avec un utilisateur de test appelé « Britta Simon ».

Pour configurer et tester l’authentification unique Azure AD avec People, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test People](#creating-a-people-test-user)** pour avoir un équivalent de Britta Simon dans People lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application People.

**Pour configurer l’authentification unique Azure AD avec People, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **People**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    [Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à People**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_04.png)
   
    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par les utilisateurs pour se connecter à votre application People au format suivant : **« https://<nom\_entreprise>.peoplehr.com/ »**.
   
    b. Si vous ne connaissez pas l’URL de votre locataire, contactez l’équipe de support People via [customerservices@peoplehr.com](mailto:customerservices@peoplehr.com) pour l’obtenir.
   
    c. Dans la zone de texte **Identificateur**, tapez l’URL du locataire.
   
    d. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : « **https://itgs.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx**".
   
    e. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur People**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_05.png)
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Next**.
5. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire People en tant qu’administrateur.
   
    a. Dans le menu sur le côté gauche, cliquez sur **Paramètres**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_001.png)
   
    b. Cliquez sur **Company** (Entreprise).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_002.png)
   
    c. Dans **Upload 'Single Sign On' SAML meta-data file** (Charger le fichier de métadonnées SAML Authentification unique), cliquez sur **Browse** (Parcourir) pour charger le fichier de métadonnées téléchargé.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_003.png)
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test People dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur **Ajouter un utilisateur** dans la barre d’outils située en bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_04.png)
5. Dans la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Dans la page de boîte de dialogue **Profil utilisateur**, procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **Prénom**, entrez **Britta**.
   
   b. Dans la zone de texte **Nom**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_07.png)
8. Dans la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.

### Création d’un utilisateur de test People
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans People. People ne prenant pas en charge l’approvisionnement juste-à-temps, vous devez contacter l’équipe de support People pour créer un utilisateur manuellement.

### Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à People.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à People, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **People**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-people-tutorial/tutorial_people_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Quand vous cliquez sur la vignette People dans le volet d’accès, vous devez être connecté automatiquement à votre application People.

## Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-people-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-people-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-people-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-people-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-people-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-people-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-people-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-people-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-people-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-people-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-people-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-people-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-people-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->