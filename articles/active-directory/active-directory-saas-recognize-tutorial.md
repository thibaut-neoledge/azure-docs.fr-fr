---
title: "Didacticiel : intégration d’Azure Active Directory à Recognize | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Recognize."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43e9febd4bc230db69b55ae3f5bb13ff17b2e3f6


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Didacticiel : Intégration d’Azure Active Directory à Recognize
L’objectif de ce didacticiel est de vous montrer comment intégrer Recognize à Azure Active Directory (Azure AD).

L’intégration de Recognize à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Recognize
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Recognize (par le biais de l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables
Pour configurer l’intégration d’Azure AD à Recognize, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Recognize pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Recognize à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-recognize-from-the-gallery"></a>Ajout de Recognize à partir de la galerie
Pour configurer l’intégration de Recognize à Azure AD, vous devez ajouter Recognize à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Recognize à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Recognize**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. Dans le volet des résultats, sélectionnez **Recognize**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Recognize au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Recognize équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Recognize associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Recognize.

Pour configurer et tester l’authentification unique Azure AD avec Recognize, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Recognize](#creating-a-recognize-test-user)** pour avoir un équivalent de Britta Simon dans Recognize lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Recognize.

**Pour configurer l’authentification unique Azure AD avec Recognize, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Recognize**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Recognize ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
   
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://recognizeapp.com/<your-domain>/saml/sso`.
   
    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://recognizeapp.com/<your-domain>/saml/metadata`.
   
    c. Cliquez sur **Suivant**
   
   > [!NOTE]
   > Si vous ne connaissez pas sur ces URL, tapez les exemples d’URL de l’exemple de modèle. Pour obtenir ces valeurs, vous pouvez vous référer à l’étape 9 pour plus d’informations ou contacter l’équipe de support Recognize via <mailto:support@recognizeapp.com>.
   > 
   > 
4. Dans la page **Configurer l’authentification unique sur Recognize**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Recognize en tant qu’administrateur.
6. Dans l’angle supérieur droit, cliquez sur **Menu**. Accédez à **Administrateur d’entreprise**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. Dans le volet de navigation gauche, cliquez sur **Paramètres**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. Dans la section **Paramètres d’authentification unique** , procédez comme suit.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
   
    a. Pour **Activer l’authentification unique**, sélectionnez **Activé**.
   
    b. Dans la zone de texte **ID de l’entité IDP**, copiez la valeur de **URL de l’émetteur** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    c. Dans la zone de texte **URL cible d’authentification unique**, copiez la valeur de **URL du service d’authentification unique** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    d. Dans la zone de texte **URL cible de déconnexion unique**, copiez la valeur de **URL du service de déconnexion unique** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    e. Ouvrez votre fichier de certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat**. 
   
    f. Cliquez sur le bouton **Enregistrer les paramètres**. 
9. En regard de la section **Paramètres d’authentification unique**, copiez l’URL sous **URL de métadonnées du fournisseur de services**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. Ouvrez le **lien URL de métadonnées** dans un navigateur vide pour télécharger le document de métadonnées. Utilisez ensuite la valeur EntityDescriptor que Recognize vous a fourni pour **Identificateur** dans la boîte de dialogue **Configurer les paramètres d’application**.
    
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]
12. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-recognize-test-user"></a>Création d’un utilisateur de test Recognize
Pour se connecter à Recognize, les utilisateurs d’Azure AD doivent être approvisionnés dans Recognize. Dans le cas de Recognize, l’approvisionnement est une tâche manuelle.

Cette application ne prend pas en charge l’approvisionnement SCIM, mais inclut une autre synchronisation de l’autre utilisateur qui approvisionne les utilisateurs. 

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site d’entreprise Recognize en tant qu’administrateur.
2. Dans l’angle supérieur droit, cliquez sur **Menu**. Accédez à **Administrateur d’entreprise**.
3. Dans le volet de navigation gauche, cliquez sur **Paramètres**.
4. Dans la section **Synchronisation de l’utilisateur**, procédez comme suit.
   
   ![New User](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")
   
   a. Pour l’option **Synchronisation activée**, sélectionnez **Activé**.
   
   b. Pour **Choisir le fournisseur de synchronisation**, sélectionnez **Microsoft / Office 365**.
   
   c. Cliquer sur **Exécuter la synchronisation de l’utilisateur**

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Recognize.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Recognize, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Recognize**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Recognize dans le volet d’accès, vous devez être connecté automatiquement à votre application Recognize.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


