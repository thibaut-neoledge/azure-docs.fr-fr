---
title: "Didacticiel : Intégration d’Azure Active Directory à SAP Cloud Platform Identity Authentication | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud Platform Identity Authentication."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.contentlocale: fr-fr
ms.lasthandoff: 09/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Didacticiel : Intégration d’Azure Active Directory à SAP Cloud Platform Identity Authentication

Dans ce didacticiel, vous allez apprendre à intégrer SAP Cloud Platform Identity Authentication à Azure Active Directory (Azure AD). SAP Cloud Platform Identity Authentication est utilisé comme IdP proxy pour accéder aux applications SAP avec Azure AD comme IdP principal.

L’intégration de SAP Cloud Platform Identity Authentication avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à l’application SAP.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement aux applications SAP via l’authentification unique (SSO) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SAP Cloud Platform Identity Authentication, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP Cloud Platform Identity Authentication avec l’authentification unique

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

Avant de rentrer dans les détails techniques, il est essentiel de comprendre les concepts que vous allez voir. La fédération de SAP Cloud Platform Identity Authentication et d’Azure Active Directory vous permet d’implémenter l’authentification unique entre les applications ou les services protégés par AAD (comme IdP) et les applications et services SAP protégés par SAP Cloud Platform Identity Authentication.

Actuellement, SAP Cloud Platform Identity Authentication remplit la fonction de fournisseur d’identité proxy pour les applications SAP. Azure Active Directory joue en retour le rôle de fournisseur d’identité principal dans cette configuration. 

En voici une illustration :

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Avec cette configuration, votre client SAP Cloud Platform Identity Authentication est configuré en tant qu’application approuvée dans Azure Active Directory. 

Toutes les applications et tous les services SAP que vous souhaitez protéger de cette façon sont ensuite configurés dans la console de gestion SAP Cloud Platform Identity Authentication !

Cela signifie que l’autorisation d’accorder l’accès aux services et applications SAP doit avoir lieu dans SAP Cloud Platform Identity Authentication pour ce type de configuration (contrairement à la configuration des autorisations dans Azure Active Directory).

Si vous configurez l’authentification SAP Cloud Platform Identity Authentication en tant qu’application sur la Place de marché Azure Active Directory, vous n’avez pas besoin de gérer la configuration des revendications individuelles / des assertions et transformations SAML nécessaires pour produire un jeton d’authentification valide pour les applications SAP.

>[!NOTE] 
>Actuellement, l’authentification unique web n’a été testée que par les deux parties. Les flux nécessaires à la communication application-API ou API-API devraient fonctionner mais n’ont pas encore été testés. Ils seront testés dans le cadre des activités à venir.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie
Pour configurer l’intégration de SAP Cloud Platform Identity Authentication dans Azure AD, vous devez ajouter SAP Cloud Platform Identity Authentication à votre liste d’applications SaaS managées à partir de la galerie.

**Pour ajouter SAP Cloud Platform Identity Authentication à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SAP Cloud Platform Identity Authentication**, sélectionnez **SAP Cloud Platform Identity Authentication** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SAP Cloud Platform Identity Authentication dans la liste de résultats](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform Identity Authentication à partir d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Cloud Platform Identity Authentication équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SAP Cloud Platform Identity Authentication associé doit être établi.

Dans SAP Cloud Platform Identity Authentication, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir le lien.

Pour configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform Identity Authentication, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créez un utilisateur de test SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user)** pour avoir un équivalent de Britta Simon dans SAP Cloud Platform Identity Authentication qui soit associé à la représentation de l’utilisateur Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application SAP Cloud Platform Identity Authentication.

**Pour configurer l’authentification unique d’Azure AD avec SAP Cloud Platform Identity Authentication, suivez les étapes ci-dessous :**

1. Dans le portail Azure, sur la page d’intégration des applications **SAP Cloud Platform Identity Authentication**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Dans la section **Domaine et URL SAP Cloud Platform Identity Authentication**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité (IDP)** :

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’identificateur réel. Contactez l’[équipe de support du client SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) pour obtenir cette valeur. Si vous ne connaissez pas cette valeur, suivez la documentation SAP Cloud Platform Identity Authentication dans [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Cliquez sur **Afficher les paramètres d’URL avancés**. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Contactez l’[équipe de support du client SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) pour obtenir cette valeur.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. L’application SAP Cloud Platform Identity Authentication attend les assertions SAML dans un format précis. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple : 

    ![Configurer l’authentification unique](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Dans la section **Attributs utilisateur**, sur la boîte de dialogue **Authentification unique**, si votre application SAP attend un attribut, par exemple « firstName ». Dans la boîte de dialogue des attributs du jeton SAML, ajoutez l’attribut « firstName ».

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Nom**, tapez le nom de l’attribut « firstName ».
    
    c. Dans la liste **Valeur**, sélectionnez la valeur d’attribut « user.givenname ».
    
    d. Cliquez sur **OK**.

8. Cliquez sur le bouton **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. Dans la section **Configuration de SAP Cloud Platform Identity Authentication**, cliquez sur **Configurer SAP Cloud Platform Identity Authentication** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Pour configurer l’authentification unique sur votre application, accédez à la console d’administration de SAP Cloud Platform Identity Authentication. L’URL suit le modèle suivant : `https://<tenant-id>.accounts.ondemand.com/admin`. Ensuite, suivez la documentation sur SAP Cloud Platform Identity Authentication pour [configurer Microsoft Azure AD en tant que fournisseur d’identité d’entreprise sur SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. Dans le portail Azure, cliquez sur le bouton **Enregistrer**.

12. Effectuez les étapes suivantes uniquement si vous souhaitez ajouter et activer l’authentification unique (SSO) pour une autre application SAP. Répétez les étapes décrites dans la section « Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie » pour ajouter une autre instance de SAP Cloud Platform Identity Authentication.

13. Dans le portail Azure, sur la page d’intégration des applications **SAP Cloud Platform Identity Authentication**, cliquez sur **Authentification liée**.

    ![Configurer l’authentification liée](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Enregistrez la configuration.

>[!NOTE] 
>La nouvelle application utilisera la configuration de l’authentification unique (SSO) de l’application SAP précédente. Veillez à utiliser les mêmes fournisseurs d’identité d’entreprise dans la console d’administration de SAP Cloud Platform Identity Authentication.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Créer un utilisateur de test SAP Cloud Platform Identity Authentication

Vous n’avez pas besoin de créer un utilisateur sur SAP Cloud Platform Identity Authentication. Les utilisateurs qui se trouvent dans le magasin d’utilisateurs Azure AD peuvent utiliser la fonctionnalité d’authentification unique (SSO).

SAP Cloud Platform Identity Authentication prend en charge l’option de fédération des identités. Cette option permet à l’application de vérifier si les utilisateurs authentifiés par le fournisseur d’identité d’entreprise sont présents dans le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication. 

Dans le paramètre par défaut, l’option de fédération des identités est désactivée. Si la fédération des identités est activée, seuls les utilisateurs importés dans SAP Cloud Platform Identity Authentication peuvent accéder à l’application. 

Pour savoir comment activer ou désactiver la fédération des identités avec SAP Cloud Platform Identity Authentication, consultez la page Activer la fédération des identités avec SAP Cloud Platform Identity Authentication dans la section [Configurer la fédération des identités avec le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à SAP Cloud Platform Identity Authentication.

![Affecter le rôle utilisateur][200] 

**Pour affecter Britta Simon à SAP Cloud Platform Identity Authentication, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter l’utilisateur][201] 

2. Dans la liste des applications, sélectionnez **SAP Cloud Platform Identity Authentication**.

    ![Lien SAP Cloud Platform Identity Authentication dans la liste Applications](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SAP Cloud Platform Identity Authentication dans le volet d’accès, vous êtes automatiquement connecté à votre application SAP Cloud Platform Identity Authentication.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png


