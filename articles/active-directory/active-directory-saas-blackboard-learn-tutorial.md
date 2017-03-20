---
title: "Didacticiel : Intégration d&quot;Azure Active Directory à Blackboard Learn | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Blackboard Learn."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0247e7c8f3803e27b4304dae6af11f18432e76b0
ms.openlocfilehash: 92ee9f08ff85a85a08a2d1e021cdb7afec0c6de7
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Didacticiel : Intégration d'Azure Active Directory à Blackboard Learn
Dans ce didacticiel, vous allez apprendre à intégrer Blackboard Learn à Azure Active Directory (Azure AD).

L’intégration de Blackboard Learn dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Blackboard Learn.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Blackboard Learn par le biais de l’authentification unique (SSO) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Blackboard Learn, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement actif à connexion unique (SSO) à la plate-forme cloud Blackboard Learn

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de Blackboard Learn à partir de la galerie
* Configuration et test de l’authentification unique (SSO) Azure AD

## <a name="add-blackboard-learn-from-the-gallery"></a>Ajouter Blackboard Learn à partir de la galerie
Pour configurer l’intégration de Blackboard Learn à Azure AD, vous devez ajouter Blackboard Learn de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Blackboard Learn à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Blackboard Learn**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_01.png)
7. Dans le volet de résultats, sélectionnez **Blackboard Learn**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Blackboard Learn avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Blackboard Learn équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Blackboard Learn associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Blackboard Learn.

Pour configurer et tester l’authentification unique Azure AD avec Blackboard Learn, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Blackboard Learn](#creating-a-blackboard-learn-test-user)** pour avoir un équivalent de Britta Simon dans Blackboard Learn lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Blackboard Learn.

L’application Blackboard Learn attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attribut** de l’application. La capture d’écran suivante montre un exemple : 

![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_51.png) 

**Pour configurer l’authentification unique Azure AD avec Blackboard Learn, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Blackboard Learn**, dans le menu situé en haut, cliquez sur **Attributs**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_80.png) 
2. Dans la boîte de dialogue **Attributs du jeton SAML** , pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit : Nous avons mappé Userprincipalname comme seul attribut utilisateur ici, mais vous pouvez le mapper à la valeur appropriée qui identifie de façon unique l’utilisateur dans l’organisation et se mappe avec le champ de nom d’utilisateur Blackboard Learn.
   
   | Nom de l'attribut | Valeur de l’attribut |
   | --- | --- |
   | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |user.userprincipalname |

    1. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_81.png) 
    2. Dans la zone de texte **Nom d’attribut** , tapez le nom d’attribut indiqué pour cette ligne.
    3. Dans la liste **Valeur d’attribut** , sélectionnez la valeur d’attribut indiquée pour cette ligne.
    4. Cliquez sur **Terminé**.    

1. Dans le portail Classic, dans la page d’intégration d’application **Blackboard Learn**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Blackboard Learn ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_04.png) 
    1. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Blackboard Learn au format suivant : **https://\<nom entreprise-pricing\>.blackboard.com/**.
    2. Cliquez sur **Next**.
4. Dans la page **Configurer l’authentification unique sur Blackboard Learn** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_05.png)
    1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
    2. Cliquez sur **Suivant**.
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Blackboard Learn et fournissez-lui les métadonnées téléchargées.
6. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_04.png)   
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_05.png) 
    1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
    2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
    3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_06.png) 
   1. Dans la zone de texte **First Name**, tapez **Britta**.  
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_08.png) 
    1. Notez la valeur du **Nouveau mot de passe**.
    2. Cliquez sur **Terminé**.   

### <a name="create-an-blackboard-learn-test-user"></a>Créer un utilisateur de test Blackboard Learn
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Blackboard Learn. 

L'application Blackboard Learn prend en charge la configuration de l'utilisateur au moment opportun. Vérifiez que vous avez configuré les revendications comme décrit dans la section **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)**

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Blackboard Learn.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Blackboard Learn, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail classique, dans l’affichage du répertoire, cliquez sur l’option **Applications** figurant dans le menu supérieur.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Blackboard Learn**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Prise en charge de l’application Blackboard Learn Lorsque vous cliquez sur la vignette Blackboard Learn dans le volet d’accès, vous devez être connecté automatiquement à votre application Blackboard Learn.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_205.png

