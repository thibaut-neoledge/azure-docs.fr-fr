---
title: "Didacticiel : Intégration d’Azure Active Directory dans Lifesize Cloud | Documentation Microsoft"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lifesize Cloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à Lifesize Cloud
Dans ce didacticiel, vous allez apprendre à intégrer Lifesize Cloud à Azure Active Directory (Azure AD).

L’intégration de Lifesize Cloud dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Lifesize Cloud
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Lifesize Cloud via l’authentification unique (SSO) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Lifesize Cloud, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Lifesize Cloud pour lequel l’authentification unique (SSO) est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de Lifesize Cloud à partir de la galerie
* Configuration et test de l’authentification unique (SSO) Azure AD

## <a name="add-lifesize-cloud-from-the-gallery"></a>Ajouter Lifesize Cloud à partir de la galerie
Pour configurer l’intégration de Lifesize Cloud à Azure AD, vous devez ajouter Lifesize Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Lifesize Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **Lifesize Cloud**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. Dans le volet de résultats, sélectionnez **Lifesize Cloud**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Lifesize Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Lifesize Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Lifesize Cloud associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Lifesize Cloud.

Pour configurer et tester l’authentification unique avec Azure AD avec Lifesize Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Lifesize Cloud](#creating-a-lifesize-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Lifesize Cloud lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique (SSO) Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Lifesize Cloud.

**Pour configurer l’authentification unique Azure AD avec Lifesize Cloud, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration de l’application **Lifesize Cloud**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Lifesize Cloud**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. Dans la zone de texte **URL d’authentification**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Lifesize Cloud au format suivant : **https://login.lifesizecloud.com/ls/?acs**.
  2. Cliquez sur **Suivant**.
4. Sur la page **Configurer l’authentification unique sur Lifesize Cloud**, procédez comme suit :
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   2. Cliquez sur **Suivant**.
5. Pour configurer SSO pour votre application, connectez-vous à l’application Lifesize Cloud avec les privilèges d’administrateur.
6. Dans le coin supérieur droit, cliquez sur votre nom, puis cliquez sur **Paramètres avancés**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. Dans les Paramètres avancés, cliquez maintenant sur le lien **Configuration de l’authentification unique**. La page Configuration de l’authentification unique de l’instance s’ouvre.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. Maintenant, configurez les valeurs suivantes dans l’interface utilisateur de configuration de l’authentification unique.    
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. Copiez la valeur de l’URL de l’émetteur à partir d’Azure et collez-la dans la zone de texte **Émetteur de fournisseur d’identité**. 
  2. Copiez la valeur de l’URL de connexion distante à partir d’Azure AD et collez-la dans la zone de texte **URL de connexion**.   
  3. Ouvrez le certificat téléchargé dans le bloc-notes et copiez le contenu du certificat, en excluant les lignes Begin Certificate et le End Certificate, puis collez-le dans la zone de texte **Certificat X.509**.
  4. Dans le mappage d’attribut SAML pour la zone de texte **Prénom**, saisissez la valeur **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
  5. Dans le mappage d’attribut SAML pour la zone de texte **Nom**, saisissez la valeur **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
  6. Dans le mappage d’attribut SAML pour la zone de texte **E-mail**, saisissez la valeur **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
9. Pour vérifier la configuration, vous pouvez cliquer sur le bouton **Test**.
   
   >[!NOTE]
   >Pour que les tests se déroulent correctement, vous devez exécuter l’assistant de configuration dans Azure AD et également fournir l’accès aux utilisateurs ou groupes autorisés à effectuer le test.
   >  
10. Activez l’authentification unique en appuyant sur le bouton **Activer l’authentification unique**.
11. Maintenant, cliquez sur le bouton **Mettre à jour** afin que tous les paramètres soient enregistrés. Cela génère la valeur RelayState. Copiez la valeur RelayState qui est générée dans la zone de texte. Nous aurons besoin de cette valeur lors des prochaines étapes.
12. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
   ![Authentification unique Azure AD][10]
13. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]
    
**Connectez-vous au portail de gestion Azure :**

1. Connectez-vous à **https://portal.azure.com** à l’aide des informations d’identification d’administrateur.
2. Dans le volet de navigation de gauche, cliquez sur **Plus de services**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. Recherchez Azure Active Directory et cliquez sur le lien **Azure Active Directory**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. Vous trouverez toutes vos applications SaaS en cliquant sur le bouton **Applications d’entreprise**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. Cliquez maintenant sur le lien **Toutes les applications** dans le panneau suivant.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. Recherchez l’application Lifesize pour laquelle vous souhaitez configurer le RelayState. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. Cliquez maintenant sur le lien **Authentification unique** dans le panneau.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. Vous verrez la case **Afficher les paramètres d’URL avancés**. Cliquez sur la case à cocher.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. Maintenant, configurez le RelayState pour l’application avec la valeur que vous voyez sur la page de configuration de l’authentification unique de l’application Lifesize. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. Enregistrez les paramètres.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. Dans la zone de texte **First Name**, tapez **Britta**.   
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. Notez la valeur du **Nouveau mot de passe**.
    2. Cliquez sur **Terminé**.   

### <a name="create-an-lifesize-cloud-test-user"></a>Créer un utilisateur de test Lifesize Cloud
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Lifesize Cloud. Lifesize Cloud ne prend pas en charge l’approvisionnement automatique des utilisateurs. Après une authentification réussie à Azure AD, l’utilisateur sera automatiquement approvisionné dans l’application. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Lifesize Cloud.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Lifesize Cloud, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Lifesize Cloud**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Lifesize Cloud dans le volet d’accès, vous devez être connecté automatiquement à votre application Lifesize Cloud.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

