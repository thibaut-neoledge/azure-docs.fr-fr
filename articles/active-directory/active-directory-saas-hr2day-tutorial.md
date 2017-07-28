---
title: "Didacticiel : Intégration d’Azure Active Directory avec HR2day by Merces | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et HR2day by Merces."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 456ef3620c1d7bb6ab2bf09b094f977e46cf2ed6
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Didacticiel : Intégration d’Azure Active Directory avec HR2day by Merces

Dans ce didacticiel, vous allez apprendre à intégrer HR2day by Merces à Azure Active Directory (Azure AD).

L’intégration de HR2day dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à HR2day by Merces.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à HR2day by Merces (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec HR2day by Merces, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement HR2day by Merces pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de HR2day by Merces à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Ajout de HR2day by Merces à partir de la galerie
Pour configurer l’intégration de HR2day by Merces avec Azure AD, vous devez ajouter HR2day by Merces à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HR2day by Merces à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **HR2day by Merces**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. Dans le panneau de résultats, sélectionnez **HR2day by Merces**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec HR2day by Merces avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HR2day by Merces équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur HR2day associé doit être établie.

Dans HR2day by Merces, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec HR2day by Merces, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur test HR2day by Merces](#creating-an-hr2day-by-merces-test-user)** : pour avoir un équivalent de Britta Simon dans HR2day by Merces lié à la représentation de l’utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application HR2day by Merces.

**Pour configurer l’authentification unique Azure AD avec HR2day by Merces, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **HR2day by Merces**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. Dans la section **Domaine et URL HR2day by Merces**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.force.com/<instancename>`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://hr2day.force.com/<companyname>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support client HR2day by Merces](mailto:servicedesk@merces.nl). 
 


4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Cette section explique comment permettre aux utilisateurs de s’authentifier sur HR2day by Merces avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

    Votre application HR2day by Merces attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre un exemple : 

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Avant de pouvoir configurer votre assertion SAML, vous devez contacter [l’équipe du support client HR2day by Merces](mailto:servicedesk@merces.nl) et lui demander d’affecter la valeur d’attribut d’identificateur unique à votre client. Vous avez besoin de cette valeur pour exécuter les étapes de la section suivante.  

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme indiqué sur l’image et procédez comme suit :
    
      | Nom de l'attribut    |   Valeur de l’attribut |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail],"102938475Z","@" |
    
      a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Nom**, saisissez **« ATTR_LOGINCLAIM »**.

    c. Dans la liste **Valeur**, sélectionnez **Join()**.

    d. Dans la liste **String1**, sélectionnez **User.mail**.

    e. Dans la zone de texte **String2**, saisissez **l’identificateur unique** fourni par votre équipe HR2day.

    f. Dans la zone de texte **Séparateur**, saisissez **@**.
    
    g. Cliquez sur **OK**.

7. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. Dans la section**Configuration HR2day by Merces**, cliquez sur **Configurer HR2day by Merces** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Pour configurer l’authentification unique (SSO) pour votre application, contactez votre [équipe du support technique HR2day by Merces](mailTo:servicedesk@merces.nl) et joignez le fichier **Certificat (Base64)** à votre e-mail. Indiquez également **l’URL de déconnexion, l’ID de l’entité SAML et l’URL du service d’authentification unique SAML**. Vous pouvez ainsi les configurer pour l’intégration de l’authentification unique.

    > [!NOTE]
    > N’oubliez pas d’indiquer à l’équipe de Merces que cette intégration requiert la définition de l’ID d’entité en suivant ce modèle **https://hr2day.force.com/INSTANCENAME**
    > 
    > 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-hr2day-by-merces-test-user"></a>Création d’un utilisateur test HR2day by Merces

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans HR2day by Merces. Collaborez avec [l’équipe du support client HR2day by Merces](mailto:servicedesk@merces.nl) pour ajouter des utilisateurs dans le compte HR2day. 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support HR2day by Merces](mailto:servicedesk@merces.nl).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HR2day by Merces.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à HR2day by Merces, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **HR2day by Merces**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Lorsque vous cliquez sur la vignette HR2day by Merces dans le volet d’accès, vous devez être connecté automatiquement à votre application HR2day by Merces.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png


