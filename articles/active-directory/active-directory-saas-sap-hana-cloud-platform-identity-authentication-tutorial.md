---
title: "Didacticiel : Intégration d’Azure Active Directory à SAP HANA Cloud Platform Identity Authentication | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP HANA Cloud Platform Identity Authentication."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 18fa3fd2fc9bb68b9e68c0c5ac5663a8a8348fd7
ms.openlocfilehash: e02cb1916c65d137dc06b1ce6a9dd9621a31b56c


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>Didacticiel : Intégration d’Azure Active Directory à SAP HANA Cloud Platform Identity Authentication

Dans ce didacticiel, vous allez apprendre à intégrer SAP HANA Cloud Platform Identity Authentication à Azure Active Directory (Azure AD). SAP HANA Cloud Platform Identity Authentication est utilisé comme IdP proxy pour accéder aux applications SAP avec Azure AD comme IdP principal.

L’intégration de SAP HANA Cloud Platform Identity Authentication avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à l’application SAP.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement aux applications SAP (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec SAP HANA Cloud Platform Identity Authentication, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement **SAP HANA Cloud Platform Identity Authentication** compatible avec l’authentification unique


>[!NOTE] 
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).



## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter SAP HANA Cloud Platform Identity Authentication à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

Avant de rentrer dans les détails techniques, il est essentiel de comprendre les concepts que vous allez voir. La fédération SAP HANA Cloud Platform Identity Authentication et Azure Active Directory vous permet d’implémenter l’authentification unique entre les applications ou les services protégés par AAD (comme IdP) et les applications et services SAP protégés par SAP HANA Cloud Platform Identity Authentication.

Actuellement, SAP HANA Cloud Platform Identity Authentication remplit la fonction de fournisseur d’identité proxy pour les applications SAP. Azure Active Directory joue en retour le rôle de fournisseur d’identité principal dans cette configuration. En voici une illustration :    

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Avec cette configuration, votre client SAP HANA Cloud Platform Identity Authentication sera configuré en tant qu’application approuvée dans Azure Active Directory. 

Toutes les applications et tous les services SAP que vous souhaitez protéger de cette façon sont ensuite configurés dans la console de gestion SAP HANA Cloud Platform Identity Authentication !

Il est très important de bien comprendre ce point ! Cela signifie que l’autorisation d’accorder l’accès aux services et applications SAP doit avoir lieu dans SAP HANA Cloud Platform Identity Authentication pour ce type de configuration (contrairement à la configuration des autorisations dans Azure Active Directory).

Si vous configurez l’authentification SAP HANA Cloud Platform Identity Authentication en tant qu’application sur la Place de marché Azure Active Directory, vous n’avez pas besoin de gérer la configuration nécessaire aux revendications individuelles / les assertions et transformations SAML nécessaires pour produire un jeton d’authentification valide pour les applications SAP.

>[!NOTE] 
>Actuellement, l’authentification unique web n’a été testée que par les deux parties. Les flux nécessaires à la communication application-API ou API-API devraient fonctionner mais n’ont pas encore été testés. Ils seront testés dans le cadre des activités à venir. La documentation sera mise à jour en conséquence après validation.

## <a name="adding-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>Ajouter SAP HANA Cloud Platform Identity Authentication à partir de la galerie
Pour configurer l’intégration de SAP HANA Cloud Platform Identity Authentication dans Azure AD, vous devez ajouter SAP HANA Cloud Platform Identity Authentication à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter SAP HANA Cloud Platform Identity Authentication à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le [**Portail de gestion Azure**](https://portal.azure.com), dans le panneau de navigation de gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Puis allez à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **SAP HANA Cloud Platform Identity Authentication**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_01.png)

5. Dans le volet de résultats, sélectionnez **SAP HANA Cloud Platform Identity Authentication**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP HANA Cloud Platform Identity Authentication, à partir d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP HANA Cloud Platform Identity Authentication équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur SAP HANA Cloud Platform Identity Authentication associé doit être établi.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans SAP HANA Cloud Platform Identity Authentication.

Pour configurer et tester l’authentification unique Azure AD avec SAP HANA Cloud Platform Identity Authentication, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SAP HANA Cloud Platform Identity Authentication](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user) ** pour avoir un équivalent de Britta Simon dans SAP HANA Cloud Platform Identity Authentication qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail de gestion Azure et configurer l’authentification unique dans votre application SAP HANA Cloud Platform Identity Authentication.

L’application SAP HANA Cloud Platform Identity Authentication attend les assertions SAML dans un format précis. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_03.png)

**Pour configurer l’authentification unique d’Azure AD avec SAP HANA Cloud Platform Identity Authentication, suivez les étapes ci-dessous :**

1. Dans le Portail de gestion Azure, sur la page d’intégration des applications **SAP HANA Cloud Platform Identity Authentication**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique][5]

3. Dans la section **Attributs utilisateur**, sur la boîte de dialogue **Authentification unique**, si votre application SAP attend un attribut, par exemple « firstName ». Dans la boîte de dialogue des attributs du jeton SAML, ajoutez l’attribut « firstName ».

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique][6]

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_05.png)
    
    b. Dans la zone de texte **Nom de l’attribut**, tapez le nom de l’attribut « firstName ».

    c. Dans la liste **Valeur de l’attribut**, sélectionnez la valeur d’attribut « user.givenname ».
    
    d. Cliquez sur **OK**.


4. Dans la section **Domaine et URL SAP HANA Cloud Platform Identity Authentication**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_06.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL de connexion de l’application SAP.

    b. Dans la zone de texte **Identificateur**, tapez la valeur au format suivant : `<tenant-id>.accounts.ondemand.com`. Si vous ne connaissez pas cette valeur, suivez la documentation SAP HANA Cloud Platform Identity Authentication sur [Configuration du client SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).


5. Dans la section **Configuration SAP HANA Cloud Platform Identity Authentication**, cliquez sur **Configurer SAP HANA Cloud Platform Identity Authentication** pour ouvrir la boîte de dialogue **Configurer l’authentification**. Ensuite, cliquez sur **Métadonnées XML SAML** et enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_08.png)


6. Pour configurer l’authentification unique sur votre application, accédez à la console d’administration de SAP HANA Cloud Platform Identity Authentication. L’URL suit le modèle suivant : `https://<tenant-id>.accounts.ondemand.com/admin`. Ensuite, suivez la documentation sur SAP HANA Cloud Platform Identity Authentication pour [configurer Microsoft Azure AD en tant que fournisseur d’identité d’entreprise sur SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

10. Dans le Portail de gestion Azure, cliquez sur le bouton **Enregistrer**.  
  

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le nouveau portail.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, tapez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**. 



### <a name="creating-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>Créer un utilisateur de test SAP HANA Cloud Platform Identity Authentication

Vous n’avez pas besoin de créer un utilisateur sur SAP HANA Cloud Platform Identity Authentication. Les utilisateurs qui se trouvent dans le magasin d’utilisateurs Azure AD peuvent utiliser la fonctionnalité d’authentification unique (SSO).

SAP HANA Cloud Platform Identity Authentication prend en charge l’option de fédération des identités. Cette option permet à l’application de vérifier si les utilisateurs authentifiés par le fournisseur d’identité d’entreprise sont présents dans le magasin d’utilisateurs de SAP HANA Cloud Platform Identity Authentication. Dans le paramètre par défaut, l’option de fédération des identités est désactivée. Si la fédération des identités est activée, seuls les utilisateurs importés dans SAP HANA Cloud Platform Identity Authentication peuvent accéder à l’application. 

Pour savoir comment activer ou désactiver la fédération des identités avec SAP HANA Cloud Platform Identity Authentication, consultez la page Activer la fédération des identités avec SAP HANA Cloud Platform Identity Authentication dans la section [Configurer la fédération des identités avec le magasin d’utilisateurs de SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP HANA Cloud Platform Identity Authentication.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SAP HANA Cloud Platform Identity Authentication, suivez les étapes ci-dessous :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP HANA Cloud Platform Identity Authentication**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_09.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque SAP HANA Cloud Platform Identity Authentication dans le volet d’accès, vous êtes automatiquement connecté à votre application SAP HANA Cloud Platform Identity Authentication.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO2-->


