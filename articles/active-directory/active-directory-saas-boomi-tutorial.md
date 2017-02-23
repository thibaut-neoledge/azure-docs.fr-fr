---
title: "Didacticiel : Intégration d’Azure Active Directory à Boomi | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Boomi."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Didacticiel : Intégration d’Azure Active Directory à Boomi

Dans ce didacticiel, vous allez apprendre à intégrer Boomi à Azure Active Directory (Azure AD).

L’intégration de Boomi dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Boomi
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Boomi (par authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Boomi, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Boomi avec authentification unique activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Boomi depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-boomi-from-the-gallery"></a>Ajout de Boomi depuis la galerie
Pour configurer l’intégration de Boomi avec Azure AD, vous devez ajouter Boomi disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Boomi à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **Boomi**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. Dans le volet de résultats, sélectionnez **Boomi**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Boomi avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Boomi équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Boomi associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Boomi.

Pour configurer et tester l’authentification unique Azure AD avec Boomi, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Boomi](#creating-a-boomi-test-user)** pour avoir un équivalent de Britta Simon dans Boomi lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Boomi.

L’application Boomi attend l’assertion SAML dans un format spécifique. Vous devez donc définir la valeur de l’attribut NameIdentifier sur l’ID de fédération de l’utilisateur. Par défaut, Azure AD utilise UserPrincipalName pour l’attribut NameIdentifier. Mais pour une intégration réussie, vous devez ajuster cette valeur pour la faire correspondre à l’ID de fédération de l’utilisateur dans Boomi. Pour modifier ce paramètre, accédez à l’onglet « **Attribut** », comme indiqué dans la capture d’écran ci-dessous. L’intégration ne fonctionnera qu’une fois le mappage correct effectué.

![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**Pour configurer l’authentification unique Azure AD avec Boomi, procédez comme suit :**

1. Dans la page d’intégration d’application **Boomi** du portail classique, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique][6] 

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Boomi**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://platform.boomi.com/sso/<account name>/saml`

    b. Cliquez sur **Suivant**.

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Notez que vous devez mettre à jour la valeur avec l’URL de réponse réelle. Contactez l’équipe de support technique de Boomi pour obtenir cette valeur.

4. Dans la page **Configurer l’authentification unique sur Boomi**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > Remarque : la valeur de revendication NameID de la réponse doit correspondre à l’ID de fédération configuré au sein du système Boomi. Nous vous invitons à collaborer avec l’équipe de support technique Boomi pour mapper l’identificateur d’utilisateur approprié dans votre organisation, en tant qu’ID de fédération. Par défaut, Azure AD définit la valeur NameIdentifier en tant que valeur UPN. Pour modifier ce paramètre, accédez à l’onglet Attribut, comme indiqué dans la capture d’écran ci-dessous. L’intégration ne fonctionnera qu’une fois le mappage correct effectué. 
     
    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur. 

6. Accédez à **Nom de la société** et allez dans **Configurer**.

7. Cliquez sur l’onglet **Options SSO** et suivez les étapes ci-dessous.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a. Cochez la case **Enable SAML Single Sign-On**.

    b. Cliquez sur **Importer** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat du fournisseur d’identité**.
    
    c. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), copiez la valeur de **URL de connexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.

    d. Dans **Federation Id Location**, sélectionnez le bouton radio **Federation Id is in NameID element of the Subject**. 

    e. Cliquez sur le bouton **Enregistrer** .

8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Authentification unique Azure AD][10]

9. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
  
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-boomi-test-user"></a>Création d’un utilisateur de test Boomi

Pour permettre aux utilisateurs Azure AD de se connecter à Boomi, vous devez les approvisionner dans Boomi. En l’occurrence, cet approvisionnement est une tâche manuelle.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

2. Après connexion, accédez à **Gestion des utilisateurs** et allez dans **Utilisateurs**.

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Utilisateurs")

3. Cliquez sur l’icône **+**, et la boîte de dialogue **Ajouter/gérer les rôles utilisateur** s’ouvre.

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Utilisateurs")

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Utilisateurs")

4. Saisissez **l’adresse de messagerie de l’utilisateur**.

5. Saisissez le **Prénom** et le **Nom** de l’utilisateur.

6. Entrez **l’ID de fédération** de l’utilisateur. Chaque utilisateur doit posséder un ID de fédération qui l’identifie de façon unique dans le compte. 

7. Affectez le rôle **Utilisateur standard** à l’utilisateur. Ne lui affectez pas le rôle d’administrateur, car cela lui donnerait l’accès normal à AtomSphere, ainsi que l’accès en authentification unique.

8. Cliquez sur **OK**.

    > [!NOTE]
    > L’utilisateur ne recevra pas de courrier électronique de notification de bienvenue contenant un mot de passe pouvant être utilisé pour se connecter au compte AtomSphere, car son mot de passe est géré via le fournisseur d’identité. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Boomi pour approvisionner des comptes d’utilisateur Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Boomi.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Boomi, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Boomi**.

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203] 

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Boomi dans le volet d’accès, vous devez être connecté automatiquement à votre application Boomi.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO2-->


