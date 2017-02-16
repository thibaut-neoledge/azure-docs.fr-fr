---
title: "Didacticiel : Intégration d’Azure Active Directory à Autotask | Microsoft Docs"
description: "Découvrez comment utiliser Autotask avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d22f107be306ad47426b89adb99f07f9d1f6617c
ms.openlocfilehash: 10292e19216e01034a597368e0f2570d49e71610


---

# <a name="tutorial-azure-active-directory-integration-with-autotask"></a>Didacticiel : Intégration d’Azure Active Directory à Autotask

L’objectif de ce didacticiel est de vous montrer comment intégrer Autotask dans Azure AD (Azure Active Directory).

L’intégration de Autotask dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Autotask. 
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Autotask (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Autotask, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Autotask pour lequel l’authentification unique est activée


> [!NOTE] 
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Autotask depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-autotask-from-the-gallery"></a>Ajout d’Autotask depuis la galerie
Pour configurer l’intégration d’Autotask à Azure AD, vous devez ajouter Autotask, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Autotask à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **Autotask**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_01.png)

7. Dans le volet des résultats, sélectionnez **Autotask**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Autotask, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Autotask équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Autotask associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Autotask. 

Pour configurer et tester l’authentification unique Azure AD avec Autotask, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Autotask](#creating-an-autotask-workplace-test-user)** pour avoir un équivalent de Britta Simon dans Autotask, associé à sa représentation dans Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Autotask.

**Pour configurer l’authentification unique Azure AD avec Autotask, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration d’application **Autotask**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
     
    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Autotask ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :

    ![Configurer l’authentification unique](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_04.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/metadata`.

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

    c. Cliquez sur **Suivant**.

4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, saisissez **l’URL de connexion** et cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_05.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<your-subdomain>.awp.autotask.net/loginsso`.

    b. Cliquez sur **Next**.

    > [!NOTE] 
    > Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

4. Sur la page **Configurer l’authentification unique sur Autotask**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_06.png)

5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’[équipe de support Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) en lui fournissant les **métadonnées** téléchargées.

6. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-an-autotask-test-user"></a>Création d’un utilisateur de test Autotask

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Autotask. Collaborez avec l’[équipe du support technique Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) pour ajouter des utilisateurs dans la plate-forme Autotask.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Autotask.
    
![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Autotask, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
    
    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Autotask**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Lorsque vous cliquez sur la vignette Autotask dans le volet d’accès, vous devez être connecté automatiquement à votre application Autotask.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


