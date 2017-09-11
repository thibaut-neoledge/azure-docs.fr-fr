---
title: "Didacticiel : Intégration d’Azure Active Directory avec SAP Business Object Cloud | Microsoft Docs)"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Business Object Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 6d517c5e302ac36e5bba2053998c75f8f4d42683
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Didacticiel : Intégration d’Azure Active Directory avec SAP Business Object Cloud

L’objectif de ce didacticiel est de vous apprendre à intégrer SAP Business Object Cloud avec Azure Active Directory (Azure AD).

Intégrer SAP Business Object Cloud avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler l’accès à SAP Business Object Cloud.
- Vous pouvez connecter automatiquement vos utilisateurs à SAP Business objet Cloud à l’aide de l’authentification unique et d’un compte utilisateur Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez l’article [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à SAP Business Object Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- SAP Business Object Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Lorsque vous testez les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Recommandations pour les étapes de test dans ce didacticiel :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajoutez SAP Business Object Cloud depuis la galerie.
2. Configurez et testez l’authentification unique Azure AD.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Ajouter SAP Business Object Cloud depuis la galerie
Pour configurer l’intégration de SAP Business Object Cloud à Azure AD, ajoutez SAP Business Object Cloud, disponible dans la galerie, à votre liste d’applications SaaS gérées.

Pour ajouter SAP Business Object Cloud depuis la galerie :

1. Dans le menu gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Page Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **SAP Business Object Cloud**.

    ![La zone de recherche](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. Dans le panneau de résultats, sélectionnez **SAP Business Object Cloud**, puis cliquez sur **Ajouter**.

    ![SAP Business Object Cloud dans la liste des résultats](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Business Object Cloud, grâce à un utilisateur de test appelé *Britta Simon*.

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Business Object Cloud équivalent dans Azure AD. Il faut établir une relation entre un utilisateur Azure AD et l’utilisateur SAP Business Object Cloud associé.

Pour établir une relation, dans SAP Business Object Cloud, assignez la valeur **Nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur**.

Pour configurer et tester l’authentification unique Azure AD avec SAP Business Object Cloud, vous devez exécuter les tâches suivantes :

1. [Configurer l’authentification unique Azure AD](#set-up-azure-ad-single-sign-on). Configure un utilisateur pour utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user). Teste l’authentification unique Azure AD avec l’utilisateur Britta Simon.
3. [Créer un utilisateur de test pour SAP Business Object Cloud](#create-an-sap-business-object-cloud-test-user). Crée un équivalent de Britta Simon dans SAP Business Object Cloud lié à la représentation d’un utilisateur Azure AD.
4. [Attribuer l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user). Configure Britta Simon pour utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on). Vérifie que la configuration fonctionne.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, activez l’authentification unique Azure AD dans le portail Azure. Ensuite, configurez l’authentification unique dans votre application SAP Business objet Cloud.

Pour configurer l’authentification Azure AD avec SAP Business objet Cloud :

1. Dans le portail Azure, dans la page d’intégration de l’application **SAP Business Object Cloud**, sélectionnez **Authentification unique**.

    ![Sélectionner l’authentification unique][4]

2. Dans la page **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Sélectionner l’authentification basée sur SAML](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Dans la section **Domaine et URL SAP Business Object Cloud**, procédez comme suit :

    1. Dans la boîte **URL de connexion**, tapez une URL dont le modèle est le suivant : 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. Dans la boîte **Identificateur**, tapez une URL dont le modèle est le suivant :
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![URL de la page URL et domaine SAP Business Object Cloud](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Les valeurs de ces URL sont uniquement à des fins de démonstration. Mettez à jour les valeurs avec les URL de connexion et de l’identificateur réels. Pour obtenir l’URL de connexion, contactez [l’équipe de support technique SAP Business Object Cloud Client](https://www.sap.com/product/analytics/cloud-analytics.support.html). Vous pouvez obtenir l’URL de l’identificateur en téléchargeant les métadonnées SAP Business Object Cloud depuis la console d’administration. Une explication sera fournie plus loin dans ce didacticiel. 

4. Sous **le certificat de signature SAML**, sélectionnez **XML des métadonnées**. Ensuite, enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Sélectionnez XML des métadonnées](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Sélectionner Enregistrer](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre du navigateur Web, ouvrez une session sur votre site d’entreprise SAP Business Object Cloud en tant qu’administrateur.

7. Sélectionnez **Menu** > **Système** > **Administration**.
    
    ![Sélectionner Menu, puis Système et Administration](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. Sous l’onglet **Sécurité**, sélectionnez l’icône (stylet) **Modifier**.
    
    ![Sous l’onglet Sécurité, sélectionner l’icône Modifier](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. Sélectionnez **Méthode d’authentification unique SAML (SSO)** comme **Méthode d’authentification**.

    ![Sélectionner Authentification unique SAML comme méthode d’authentification](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Pour télécharger les métadonnées du fournisseur de services (étape 1), sélectionnez **Télécharger**. Dans le fichier de métadonnées, recherchez et copiez la valeur **entityID**. Dans le portail Azure, sous **URL et domaine SAP Business Object Cloud**, collez la valeur dans la boîte **Identificateur**.

    ![Copier et coller la valeur entityID](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Pour télécharger les métadonnées du fournisseur de services (étape 2) dans le fichier que vous avez téléchargé depuis le portail Azure, sous **Charger les métadonnées du fournisseur d’identité**, sélectionnez **Charger**.  

    ![Sous Charger les métadonnées du fournisseur d’identité, sélectionnez Charger](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Dans la liste **Attribut utilisateur**, sélectionnez l’attribut utilisateur (étape 3) que vous souhaitez utiliser pour votre mise en œuvre. Cet attribut utilisateur est mappé au fournisseur d’identité. Pour entrer un attribut personnalisé sur la page de l’utilisateur, utilisez l’option **Mappage SAML personnalisé**. Ou bien, vous pouvez sélectionner **E-mail** ou **ID UTILISATEUR** en tant qu’attribut utilisateur. Dans notre exemple, nous avons sélectionné **E-mail**, car nous avons mappé la revendication de l’identificateur d’utilisateur avec l’attribut **userprincipalname** dans la section **attributs utilisateur** dans le portail Azure. Cela fournit un e-mail de l’utilisateur unique, qui est envoyé à l’application SAP Business Object Cloud dans chaque réponse SAML correcte.

    ![Sélectionner un attribut utilisateur](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Pour vérifier le compte avec le fournisseur d’identité (étape 4), dans la boîte **Informations d’identification de connexion (e-mail)**, entrez l’adresse e-mail de l’utilisateur. Ensuite, sélectionnez **Vérifier le compte**. Le système ajoute les informations d’identification de connexion au compte utilisateur.

    ![Entrer l’e-mail et sélectionner Vérifier le compte](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Sélectionnez l’icône **Enregistrer**.

    ![Icône Enregistrer](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Vous pouvez lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez votre application ! Après avoir ajouté l’application en sélectionnant **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**. Vous pouvez accéder à la documentation incorporée dans la section **Configuration** en bas de la page. Pour plus d’informations, consultez [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, créez un utilisateur de test nommé Britta Simon dans le portail Azure.

Pour créer un utilisateur de test dans Azure AD :

1. Dans le menu du gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, sélectionnez **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :
 
    1. Dans la boîte **Nom**, tapez **BrittaSimon**.

    2. Dans la boîte **Nom d’utilisateur**, tapez l’adresse e-mail de l’utilisateur Britta Simon.

    3. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    4. Sélectionnez **Créer**.

        ![Boîte de dialogue Utilisateur](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Créer un utilisateur Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Créer un utilisateur de test SAP Business Object Cloud

Les utilisateurs Azure AD doivent être approvisionnés dans SAP Business Object Cloud avant de pouvoir se connecter à SAP Business Object Cloud. Dans SAP Business Object Cloud, l’approvisionnement est une tâche manuelle.

Pour approvisionner un compte d’utilisateur :

1. Connectez-vous en tant qu’administrateur à votre site d’entreprise SAP Business Object Cloud.

2. Sélectionnez **Menu** > **Sécurité** > **Utilisateurs**.

    ![Ajouter un employé](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Sur la page **Utilisateurs**, pour ajouter de nouvelles informations de l’utilisateur, sélectionnez **+**. 

    ![Page Ajouter des utilisateurs](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Effectuez ensuite les tâches suivantes :

    1. Dans la boîte **ID UTILISATEUR**, entrez l’ID utilisateur de l’utilisateur, par exemple **Britta**.

    2. Dans la boîte **PRÉNOM**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    3. Dans la boîte **NOM**, entrez le prénom de l’utilisateur, par exemple **Simon**.

    4. Dans la boîte **NOM COMPLET**, tapez le nom complet de l’utilisateur, par exemple **Britta Simon**.

    5. Dans la boîte **E-MAIL**, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    6. Dans la page **Sélectionner des rôles**, sélectionnez le rôle approprié pour l’utilisateur, puis **OK**.

      ![Sélectionner un rôle](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Sélectionnez l’icône **Enregistrer**.    


### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, autorisez l’utilisateur Britta Simon à utiliser l’authentification unique Azure AD en lui accordant l’accès utilisateur à SAP Business Object Cloud.

Pour assigner Britta Simon à SAP Business Object Cloud :

1. Dans le portail Azure, ouvrez la vue des applications, puis accédez à la vue des répertoires. Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP Business Object Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Sélectionner Utilisateurs et groupes][202] 

4. Sélectionnez **Ajouter**. Ensuite, dans la page **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![La page Ajouter une attribution][203]

5. Dans la page **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Dans la page **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

7. Dans la page **Ajouter une attribution**, sélectionnez **Attribuer**.

![Attribuer le rôle utilisateur][200] 
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, testez la configuration de l’authentification unique Azure AD à l’aide du panneau d’accès.

En cliquant sur la vignette SAP Business Object Cloud dans le panneau d’accès, vous êtes automatiquement connecté à votre application SAP Business Object Cloud.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png


