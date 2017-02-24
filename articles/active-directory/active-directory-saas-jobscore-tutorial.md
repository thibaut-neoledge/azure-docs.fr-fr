---
title: "Didacticiel : Intégration d’Azure Active Directory avec JobScore | Microsoft Docs"
description: "Apprenez à utiliser JobScore avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 30f51b32-e55c-4c66-96e8-50a2f9c2194a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7fcf4a3293da07b406216e2382e1a6d29f934f23
ms.openlocfilehash: 18ff96c76a4e2233edf734f84cce8adbf7716c8b


---

# <a name="tutorial-azure-active-directory-integration-with-jobscore"></a>Didacticiel : Intégration d’Azure Active Directory à JobScore

Dans ce didacticiel, vous allez apprendre à intégrer JobScore à Azure Active Directory (Azure AD).

L’intégration de JobScore avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à JobScore
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à JobScore (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à JobScore, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement JobScore pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de JobScore à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-jobscore-from-the-gallery"></a>Ajout de JobScore à partir de la galerie
Pour configurer l’intégration de JobScore à Azure AD, vous devez ajouter JobScore à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter JobScore à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, entrez **JobScore**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/tutorial_jobscore_001.png)

7. Dans le volet de résultats, sélectionnez **JobScore**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/tutorial_jobscore_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec JobScore avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur JobScore équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur JobScore associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans JobScore.

Pour configurer et tester l’authentification unique Azure AD avec JobScore, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test JobScore](#creating-a-jobscore-test-user)** pour avoir un équivalent de Britta Simon dans JobScore lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application JobScore.


**Pour configurer l’authentification unique Azure AD avec JobScore, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **JobScore**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à JobScore**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-jobscore-tutorial/tutorial_jobscore_02.png)

3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscore-tutorial/tutorial_jobscore_03.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://hire.jobscore.com/auth/adfs/<company name>`

    b. Cliquez sur **Suivant**.

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe de support technique de JobScore](emaiLto:support@jobscore.com) pour obtenir cette valeur.

4. Sur la page **Configurer l’authentification unique sur JobScore**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscore-tutorial/tutorial_jobscore_04.png) 

5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez [l’équipe de support JobScore](emaiLto:support@jobscore.com) en lui fournissant les **métadonnées** téléchargées.

6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
  
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jobscore-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-jobscore-test-user"></a>Création d’un utilisateur de test JobScore

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans JobScore. Collaborez avec [l’équipe du support technique JobScore](emaiLto:support@jobscore.com) pour ajouter des utilisateurs dans la plateforme JobScore.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à JobScore.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à JobScore, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **JobScore**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscore-tutorial/tutorial_jobscore_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203] 

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque JobScore dans le volet d’accès, vous devez vous connecter automatiquement à votre application JobScore.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jobscore-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


