---
title: "Didacticiel : Intégration d’Azure Active Directory avec FileCloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et FileCloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 69473976eb0a9b6a0e9aa929581c0a7cf0c8d188
ms.openlocfilehash: 49779d5d15e81a50e32b87255152d996600d23eb


---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Didacticiel : Intégration d’Azure Active Directory avec FileCloud
L’objectif de ce didacticiel est de vous montrer comment intégrer FileCloud avec Azure AD (Azure Active Directory).

L’intégration de FileCloud avec Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à FileCloud
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FileCloud (authentification unique) avec leurs comptes Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec FileCloud, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement FileCloud pour lequel l’authentification unique est activée

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

1. Ajout de FileCloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-filecloud-from-the-gallery"></a>Ajout de FileCloud à partir de la galerie
Pour configurer l’intégration de FileCloud à Azure AD, vous devez ajouter FileCloud à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter FileCloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]

6. Dans la zone de recherche, tapez **FileCloud**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_01.png)

7. Dans le volet des résultats, sélectionnez **FileCloud**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec FileCloud sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans FileCloud équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur associé dans FileCloud.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans FileCloud.

Pour configurer et tester l’authentification unique Azure AD avec FileCloud, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test FileCloud](#creating-a-filecloud-test-user)** pour avoir un équivalent de Britta Simon dans FileCloud, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application FileCloud.

**Pour configurer l’authentification unique Azure AD avec FileCloud, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration de l’application **FileCloud**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à FileCloud ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_04.png)
   
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.filecloudhosted.com`.
   
    b. Dans la zone de texte **Identificateur**, tapez `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.
   
    c. Cliquez sur **Suivant**
   
    > [!NOTE]
    > Notez que vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’équipe de support technique FileCloud via <mailto:support@codelathe.com>.
    > 
    > 

4. Dans la page **Configurer l’authentification unique sur FileCloud**, procédez comme suit, puis cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_05.png)
   
    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Next**.

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre client FileCloud en tant qu’administrateur.

6. Dans le volet de navigation gauche, cliquez sur **Paramètres**. 
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

7. Dans la section Paramètres, cliquez sur l’onglet **SSO**. 
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

8. Dans le panneau **Single Sign On (SSO) Settings** (Paramètres d’authentification unique), sélectionnez **SAML** comme **Default SSO Type** (Type d’authentification unique par défaut).
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

9. Dans la zone de texte **IdP End Point URL** (URL du point de terminaison IdP), placez la valeur de **Entity ID** (ID d’entité) du panneau **SAML Settings** (Paramètres SAML) de l’Assistant Configuration de l’application Azure AD.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

10. Ouvrez votre fichier de métadonnées téléchargé dans le bloc-notes, copiez le contenu de celui-ci dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Meta Data** (métadonnées IdP) du panneau **SAML Settings** (Paramètres SAML).
    
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

11. Cliquez sur le bouton **Enregistrer** .

12. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

13. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_05.png)
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.

6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_06.png)
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-filecloud-test-user"></a>Création d’un utilisateur de test de FileCloud
L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans FileCloud. FileCloud prend en charge le déploiement juste-à-temps, qui est l’option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à FileCloud s’il n’existe pas déjà. 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique FileCloud.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à FileCloud.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à FileCloud, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **FileCloud**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette FileCloud dans le volet d’accès, vous devez être automatiquement authentifié auprès de votre application FileCloud.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


