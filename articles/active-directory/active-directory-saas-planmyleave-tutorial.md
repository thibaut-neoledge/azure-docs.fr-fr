---
title: "Didacticiel : Intégration d’Azure Active Directory à PlanMyLeave | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et PlanMyLeave."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 822e25ab6cd94f312429260778ef13d53bbc0b5f
ms.openlocfilehash: e2d1071f08700d54616cd6b10cadbf7359aa479b


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Didacticiel : Intégration de PlanMyLeave à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer PlanMyLeave à Azure Active Directory (Azure AD).

L’intégration de PlanMyLeave à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à PlanMyLeave.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à PlanMyLeave (par le biais de l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans PlanMyLeave, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement PlanMyLeave pour lequel l’authentification unique est activée


> [!NOTE] 
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de PlanMyLeave depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-planmyleave-from-the-gallery"></a>Ajout de PlanMyLeave depuis la galerie
Pour configurer l’intégration de PlanMyLeave à Azure AD, vous devez ajouter PlanMyLeave depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter PlanMyLeave à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans le champ de recherche, entrez **PlanMyLeave**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)
    
7. Dans le volet des résultats, sélectionnez **PlanMyLeave**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_011.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec PlanMyLeave avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur PlanMyLeave équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur PlanMyLeave associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans PlanMyLeave.

Pour configurer et tester l’authentification unique Azure AD avec PlanMyLeave, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test PlanMyLeave](#creating-a-PlanMyLeave-test-user)** pour avoir un équivalent de Britta Simon dans PlanMyLeave qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application PlanMyLeave.


**Pour configurer l’authentification unique Azure AD avec PlanMyLeave, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration d’application **PlanMyLeave**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
     
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à PlanMyLeave ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png) 

3. Sur la page **Configurer les paramètres de l’application**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png) 

    - Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par les utilisateurs pour se connecter à votre application PlanMyLeave au format suivant : `https://<company-name>.planmyleave.com/Login.aspx`.
    
    > [!NOTE]
    > Notez que vous devez mettre à jour ces valeurs avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez [l’équipe de support technique PlanMyLeave](emaiLto:support@planmyleave.com).
         
4. Sur la page **Configurer l’authentification unique sur PlanMyLeave**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png)
   
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support PlanMyLeave <a href=“mailto:support@planmyleave.com”>ici</a> et fournissez-lui les éléments suivants :

    • Le fichier de **métadonnées téléchargé**
    
    • **L’URL d’authentification unique SAML**
    
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
 
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-planmyleave-test-user"></a>Création d’un utilisateur de test PlanMyLeave

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans PlanMyLeave. Contactez l’équipe de support PlanMyLeave <a href=“mailto:support@planmyleave.com”>ici</a> pour ajouter des utilisateurs dans la plateforme PlanMyLeave.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à PlanMyLeave.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à PlanMyLeave, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **PlanMyLeave**.

    ![Configurer l’authentification unique](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette PlanMyLeave dans le volet d’accès, vous êtes connecté automatiquement à votre application PlanMyLeave.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO3-->


