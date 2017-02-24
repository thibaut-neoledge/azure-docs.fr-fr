---
title: "Didacticiel : Intégration d’Azure Active Directory à Ceridian Dayforce HCM | Microsoft docs"
description: "Découvrez comment configurer l&quot;authentification unique entre Azure Active Directory et Ceridian Dayforce HCM."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: dba809c8d7021357142ec2832b4660c6fb10ec96


---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Didacticiel : Intégration d’Azure Active Directory à Ceridian Dayforce HCM
L’objectif de ce didacticiel est de vous montrer comment intégrer Ceridian Dayforce HCM à Azure Active Directory (Azure AD).  
L’intégration de Ceridian Dayforce HCM à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Ceridian Dayforce HCM.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Ceridian Dayforce HCM (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Ceridian Dayforce HCM, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Ceridian Dayforce HCM pour lequel l’authentification unique est activée

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

1. Ajout de Ceridian Dayforce HCM à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Ajout de Ceridian Dayforce HCM à partir de la galerie
Pour configurer l’intégration de Ceridian Dayforce HCM à Azure AD, vous devez ajouter Ceridian Dayforce HCM à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Ceridian Dayforce HCM à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Ceridian Dayforce HCM**.
   
    ![Applications](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)
7. Dans le volet des résultats, sélectionnez **Ceridian Dayforce HCM**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Applications](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Ceridian Dayforce HCM avec un utilisateur de test appelé « Britta Simon ».

Pour que l'authentification unique fonctionne, Azure AD doit savoir qui est l'utilisateur Ceridian Dayforce HCM équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Ceridian Dayforce HCM associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Ceridian Dayforce HCM, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Ceridian Dayforce HCM](#creating-a-ceridian-dayforce-hcm-test-user)** pour avoir un équivalent de Britta Simon dans Ceridian Dayforce HCM lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Ceridian Dayforce HCM.

Votre application Ceridian Dayforce HCM attend les assertions SAML dans un format spécifique. Collaborez avec l’équipe Dayforce HCM pour identifier tout d’abord l’identificateur utilisateur correct. Microsoft recommande d’utiliser l’attribut **« name »** sous la forme d’identificateur utilisateur. Vous pouvez gérer la valeur de cet attribut dans la boîte de dialogue **« Attribut »** . La capture d’écran suivante montre un exemple : 

![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) 

**Pour configurer l'authentification unique Azure AD avec Ceridian Dayforce HCM, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Ceridian Dayforce HCM**, dans le menu situé en haut, cliquez sur **Attributs**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png) 
2. Dans la liste des **attributs du jeton saml**, sélectionnez l’attribut de nom, puis cliquez sur **Modifier**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png) 
3. Dans la boîte de dialogue **Modifier un attribut utilisateur** , procédez comme suit :
   
    a. Dans la liste **Valeur de l’attribut** , sélectionnez l’attribut utilisateur que vous souhaitez utiliser pour votre implémentation.  
    Par exemple, si vous souhaitez utiliser EmployeeID comme identificateur d’utilisateur unique et que vous avez stocké la valeur d’attribut dans ExtensionAttribute2, sélectionnez **user.extensionattribute2**. 
   
    b. Cliquez sur **Terminé**.    
4. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  
5. Dans le portail Azure Classic, sur la page d’intégration d’application **Ceridian Dayforce HCM**, cliquez sur **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
6. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Ceridian Dayforce HCM ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) 
7. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) 

    a. Dans la zone de texte **URL d’authentification** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Ceridian Dayforce HCM. Pour les environnements de production, utilisez le format d’URL suivant : `https://sso.dayforcehcm.com/DayforcehcmNamespace`

    Pour plus de clarté, remplacez DayforcehcmNamespace par l’espace de noms de votre environnement ou votre ID d’entreprise, par exemple : `https://sso.dayforcehcm.com/contoso`

    Pour les environnements de test, utilisez le format d’URL suivant : `https://ssotest.dayforcehcm.com/DayforcehcmNamespace` 

    b. Dans la zone de texte **URL de réponse**, tapez l’URL utilisée par Azure AD pour publier la réponse.  
    Pour les environnements de production, utilisez : `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2`  
    Environnements de test, utilisez : `https://fs-test.dayforcehcm.com/sp/ACS.saml2`  

1. Dans la page **Configurer l’authentification unique sur Ceridian Dayforce HCM** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) 
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Next**.
2. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Ceridian Dayforce HCM par courrier électronique, et envoyez-lui les éléments suivants :
   
   * Le fichier de certificat téléchargé
   * **L’URL de l’émetteur**
   * **L’URL d’authentification unique SAML** 
   * **L’URL du service de déconnexion unique** 
3. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) 
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-ceridian-dayforce-hcm-test-user"></a>Création d’un utilisateur de test Ceridian Dayforce HCM
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Ceridian Dayforce HCM. 

Collaborez avec l’équipe de support technique Ceridian Dayforce HCM pour ajouter des utilisateurs à l’application Ceridian Dayforce HCM. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Ceridian Dayforce HCM.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Ceridian Dayforce HCM, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Ceridian Dayforce HCM**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Lorsque vous cliquez sur la mosaïque Ceridian Dayforce HCM dans le volet d’accès, vous devriez être connecté automatiquement à votre application Ceridian Dayforce HCM.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


