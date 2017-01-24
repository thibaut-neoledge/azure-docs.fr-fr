---
title: "Didacticiel : Intégration d’Azure Active Directory à Convercent | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Convercent."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: aec25285cd0fa2f09e21a629648e015dffbeb11d
ms.openlocfilehash: 9ea835787db25adabe9b8cb71551de49b330a6dd


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Didacticiel : Intégration d’Azure Active Directory à Convercent

Dans ce didacticiel, vous allez apprendre à intégrer Convercent à Azure Active Directory (Azure AD).

L’intégration de Convercent à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Convercent.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Convercent (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Convercent, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Convercent pour lequel l’authentification unique est activée


>[!NOTE] 
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Convercent à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-convercent-from-the-gallery"></a>Ajout de Convercent à partir de la galerie
Pour configurer l’intégration de Convercent à Azure AD, vous devez ajouter Convercent à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Convercent à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **Convercent**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

7. Dans le volet des résultats, sélectionnez **Convercent**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Convercent avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Convercent équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Convercent associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Convercent.

Pour configurer et tester l’authentification unique Azure AD avec Convercent, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Convercent](#creating-a-convercent-test-user)** : pour avoir un équivalent de Britta Simon dans Convercent lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Convercent.


**Pour configurer l’authentification unique Azure AD avec Convercent, procédez comme suit :**

1. Sur la page d’intégration d’application **Convercent** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
     
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Convercent**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png) 

3. Dans la page **Configurer les paramètres de l’application**, cliquez sur Suivant. Cela vous permet de configurer l’application en mode lancé par le fournisseur d’identité.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    >[!NOTE] 
    >Étant donné que vous configurez cette application en mode lancé par le fournisseur d’identité, vous devez configurer la valeur RelayState de l’application. Sinon, l’intégration de l’authentification unique ne fonctionnera pas. Consultez l’étape 5 pour la configuration de la valeur RelayState.

4. Si vous voulez configurer l’application en mode lancé par le fournisseur de service, cliquez sur la case **Afficher les paramètres avancés** et procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png) 

    a. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Convercent au format suivant : `https://app.convercent.com/`
    
    b. Cliquez sur **Suivant**
 
5. Si vous avez configuré l’application en mode lancé par le fournisseur d’identité, configurez la valeur RelayState pour l’application. Pour configurer la valeur RelayState dans Azure AD, procédez comme suit : 
    
    a. Connectez-vous au [portail de gestion Azure](https://portal.azure.com) en tant qu’administrateur.

    b. Dans le volet de navigation de gauche, cliquez sur **Plus de services**. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_07.png)

    c. Dans la zone de texte **Recherche**, tapez **Azure Active Directory**, puis cliquez sur le lien associé.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_08.png)

    d. Cliquez sur **Applications d’entreprise**.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_09.png)

    e. Dans la section **Gérer**, cliquez sur **Toutes les applications**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_10.png)

    f. Dans la zone de texte **Recherche**, tapez **Azure Active Directory**, puis cliquez sur le lien associé. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_11.png)

    g. Dans la section **Gérer**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_12.png)

    h. Sélectionnez **Afficher les paramètres d’URL avancés**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_13.png)
    
    i. Dans la zone de texte **Relay State** (État du relais), tapez une valeur en respectant les formats suivants : `https://app.convercent.com/`

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_14.png)

    j. Enregistrez les paramètres.

6. Sur la page **Configurer l’authentification unique sur Convercent**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_06.png)

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Next**.


6. Pour obtenir la configuration de l’authentification unique pour votre application, contactez [l’équipe de support](mailTo:support@convercent.com) Convercent et fournissez-lui les éléments suivants :

    a. Les **métadonnées** téléchargées

7. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
 
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-an-convercent-test-user"></a>Création d’un utilisateur de test Convercent

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Convercent. Collaborez avec [l’équipe de support](mailTo:support@convercent.com) Convercent pour ajouter des utilisateurs dans la plateforme Convercent.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Convercent.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Convercent, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Convercent**.

    ![Configurer l’authentification unique](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Convercent dans le volet d’accès, vous devez être connecté automatiquement à votre application Convercent.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO3-->


