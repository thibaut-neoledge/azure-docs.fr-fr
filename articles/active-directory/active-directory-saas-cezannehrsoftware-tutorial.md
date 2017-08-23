---
title: "Didacticiel : Intégration d’Azure Active Directory avec le logiciel Cezanne HR | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et le logiciel Cezanne HR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Didacticiel : Intégration d’Azure Active Directory avec le logiciel Cezanne HR

Dans ce didacticiel, vous allez apprendre à intégrer le logiciel Cezanne HR avec Azure Active Directory (Azure AD).

L’intégration du logiciel Cezanne HR dans Azure AD vous offre les avantages suivants : Vous pouvez :

- contrôler, dans Azure AD, qui a accès au logiciel Cezanne HR.
- autoriser les utilisateurs à se connecter automatiquement au logiciel Cezanne HR (par le biais d’une authentification unique) avec leurs comptes Azure AD.
- gérer vos comptes à un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration d’applications SaaS (software as a service) dans Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec le logiciel Cezanne HR, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement au logiciel Cezanne HR pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout du logiciel Cezanne HR à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Ajouter le logiciel Cezanne HR à partir de la galerie
Pour configurer l’intégration du logiciel Cezanne HR avec Azure AD, ajoutez le logiciel Cezanne HR, disponible dans la galerie, à votre liste d’applications SaaS gérées.

Pour ajouter le logiciel Cezanne HR à partir de la galerie, procédez comme suit :

1. Dans le volet gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur le bouton **Azure Active Directory**. 

    ![Le bouton « Azure Active Directory »][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Lien Toutes les applications][2]
    
3. Pour ajouter une application, en haut de la boîte de dialogue **Toutes les applications**, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Cezanne HR Software**.

    ![La zone de recherche](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Dans la liste des résultats, sélectionnez **Cezanne HR Software**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Liste des résultats](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec le logiciel Cezanne HR, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’équivalent du logiciel Cezanne HR pour l’utilisateur Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur du logiciel Cezanne HR associé.

Pour établir cette relation, assignez la valeur du **nom d’utilisateur** du logiciel Cezanne HR en tant que valeur de **nom d’utilisateur** Azure AD.

Pour configurer et tester l’authentification unique Azure AD avec le logiciel Cezanne HR, conformez-vous aux indications des sections suivantes :

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et la configurer dans votre application logicielle Cezanne HR en procédant comme suit :

1. Sur le portail Azure, dans la page d’intégration de l’application **Cezanne HR Software**, sélectionnez **Authentification unique**.

    ![Commande Authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, sous la zone **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Zone Mode](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Sous **Domaine et URL Cezanne HR Software**, procédez comme suit :

    ![Section Domaine et URL Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL dont la syntaxe est la suivante : `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL dont la syntaxe est la suivante : `https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support technique du logiciel Cezanne HR](mailto:info@cezannehr.com).

4. Sous **Certificat de signature SAML**, sélectionnez **Certificat (en base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Section Certificat de signature SAML](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. Sous **Configuration de Cezanne HR Software**, sélectionnez **Configurer Cezanne HR Software** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML** et l’URL du **service d’authentification unique SAML** à partir de la section **Référence rapide**.

    ![Section Configuration de Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire du logiciel Cezanne HR en tant qu’administrateur.

8. Dans le volet gauche, sélectionnez **Paramétrage du système**. Sélectionnez **Paramètres de sécurité** > **Configuration d’authentification unique**.

    ![Lien Configuration d’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. Dans le volet **Autoriser les utilisateurs à se connecter à l’aide de l’authentification unique**, cochez la case **SAML 2.0** et sélectionnez l’option **Configuration avancée**.

    ![Options des services d’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Sous **SAML 2.0 Identity Providers**, procédez comme suit :

    ![Section SAML 2.0 Identity Providers](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Dans la zone **Nom affiché**, entrez le nom de votre fournisseur d’identité.

    b. Dans la zone de texte **Identificateur d’identité**, collez l’**ID d’entité SAML** copiée à partir du portail Azure. 

    c. Dans la zone de liste **SAML Binding** (Liaison SAML), sélectionnez **POST**.

    d. Dans la zone de texte **Point de terminaison de service du jeton de sécurité**, collez l’URL du **service d’authentification unique SAML** que vous avez copiée dans le portail Azure. 
    
    e. Dans la zone de texte **Nom d’attribut de l’ID d’utilisateur**, entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Pour charger le certificat obtenu auprès d’Azure AD, cliquez sur le bouton **Charger**.
    
    g. Sélectionnez **OK**. 

12. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Lors de la configuration de l’application, vous pouvez lire une version abrégée des instructions précédentes sur le [portail Azure](https://portal.azure.com). Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**. Ensuite, accédez à la documentation incorporée par le biais de la section **Configuration**. 

Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez la [documentation incorporée d’Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure.

![Utilisateur de test Britta Simon][100]

Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :

1. Dans le volet gauche du **portail Azure**, cliquez sur le bouton **Azure Active Directory**.

    ![Le bouton « Azure Active Directory »](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.
    
    ![Lien Tous les utilisateurs](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    La boîte de dialogue **Tous les utilisateurs** s’ouvre.

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter**.
 
    ![Bouton « Ajouter »](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![La boîte de dialogue « Utilisateur »](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur**, tapez l’**adresse e-mail** de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur générée dans la zone **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Créer un utilisateur de test pour le logiciel Cezanne HR

Pour permettre aux utilisateurs Azure AD de se connecter au logiciel Cezanne HR, vous devez les approvisionner dans le logiciel Cezanne HR. Dans le cas du logiciel Cezanne HR, cet approvisionnement est une tâche manuelle.

Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site d’entreprise du logiciel Cezanne HR en tant qu’administrateur.

2.  Dans le volet gauche, sélectionnez **Paramétrage du système** > **Gérer les utilisateurs** > **Ajouter un nouvel utilisateur**.

    ![Lien Ajouter un nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nouvel utilisateur")

3.  Sous **Détails du candidat**, procédez comme suit :

    ![Section Détails du candidat](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nouvel utilisateur")
    
    a. Paramétrez **Utilisateur interne** sur **Désactivé**.
    
    b. Dans la zone **Prénom** , tapez le prénom de l’utilisateur, par exemple **Britta**.  
 
    c. Dans la zone **Nom** , tapez le nom de l’utilisateur, par exemple **Simon**.
    
    d. Dans la zone **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple Brittasimon@contoso.com.

4.  Sous **Informations sur le compte**, procédez comme suit :

    ![Section Informations sur le compte](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nouvel utilisateur")
    
    a. Dans la zone **Nom d’utilisateur**, tapez l’adresse e-mail de l’utilisateur, par exemple Brittasimon@contoso.com.
    
    b. Dans la zone **Mot de passe**, entrez le mot de passe de l’utilisateur.
    
    c. Dans la zone **Rôle de sécurité**, sélectionnez **Professionnel des RH**.
    
    d. Sélectionnez **OK**.

5. Dans l’onglet **Authentification unique**, sous la section **Identificateurs SAML 2.0**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilisateur")

6. Dans la zone de liste **Fournisseur d’identité**, sélectionnez votre fournisseur d’identité. Dans la zone **Identificateur d’utilisateur**, entrez l’adresse e-mail du compte de test de l’utilisateur Britta Simon.

    ![Zones Fournisseur d’identité et Identificateur d’utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilisateur")
    
7. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilisateur")

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur de test Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès au logiciel Cezanne HR.

![Accès de l’utilisateur de test][200] 

1. Dans le portail Azure, ouvrez la vue des applications, puis accédez à la vue des répertoires. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Lien Toutes les applications][201] 

2. Dans la liste des applications, sélectionnez **Cezanne HR Software**.

    ![Liste Applications](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Sélectionnez **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Lien Utilisateurs et groupes][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, dans la liste **Utilisateurs**, sélectionnez **Britta Simon**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.
    
### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette du logiciel Cezanne HR dans le panneau d’accès, vous vous connectez automatiquement à votre application logicielle Cezanne HR.

## <a name="next-steps"></a>Étapes suivantes

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png


