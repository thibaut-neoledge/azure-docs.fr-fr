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
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a09e965b6fc9b89023c09092860fcf79773a4518
ms.contentlocale: fr-fr
ms.lasthandoff: 08/12/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Didacticiel : Intégration d’Azure Active Directory avec SAP Business Object Cloud

L’objectif de ce didacticiel est de vous apprendre à intégrer SAP Business Object Cloud avec Azure Active Directory (Azure AD).

Intégrer SAP Business Object Cloud avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler l’accès à SAP Business Object Cloud
- Vous pouvez autoriser la connexion automatique à SAP Business Object Cloud (via l’authentification unique) pour vos utilisateurs avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec SAP Business Object Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP Business Object Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter SAP Business Object Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Ajouter SAP Business Object Cloud à partir de la galerie
Pour configurer l’intégration de SAP Business Object Cloud à Azure AD, vous devez ajouter SAP Business Object Cloud, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Business Object Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **SAP Business Object Cloud**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. Dans le panneau de résultats, sélectionnez **SAP Business Object Cloud**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Business Object Cloud, grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Business Object Cloud équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur SAP Business Object Cloud associé.

Dans SAP Business Object Cloud, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SAP Business Object Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SAP Business Object Cloud](#creating-an-sap-business-object-cloud-test-user)** pour avoir un équivalent de Britta Simon dans SAP Business Object Cloud lié à la représentation d’un utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SAP Business Object Cloud.

**Pour configurer l’authentification unique Azure AD avec SAP Business Object Cloud, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SAP Business Object Cloud**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Dans la section **Domaine et URL SAP Business Object Cloud**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir l’URL de connexion, contactez [l’équipe de support technique SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html). Vous pouvez également obtenir l’identificateur qui permet de télécharger les métadonnées SAP Business Object Cloud à partir de la console d’administration. La procédure est expliquée plus loin de ce didacticiel. 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre de navigateur web, ouvrez une session sur votre site d’entreprise SAP Business Object Cloud en tant qu’administrateur.

7. Accédez à **Menu > Système > Administration**
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. Sur l’onglet **Sécurité**, cliquez sur **[Modifier – icône de stylet]**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. Sélectionnez **Authentification unique (SSO) SAML** comme méthode d’authentification.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Pour télécharger les métadonnées du fournisseur de services, cliquez sur **Télécharger**. Récupérez la valeur **entityID** à partir du fichier, puis collez-la dans la zone de texte **Identificateur** dans la section **Domaine et URL SAP Business Object Cloud** sur le portail Azure.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. À l’étape **Charger les métadonnées du fournisseur d’identité**, cliquez sur le bouton **Charger** afin de charger le fichier de métadonnées téléchargé à partir du portail Azure. 

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Sélectionnez **l’attribut utilisateur** à utiliser pour votre implémentation à partir de la liste pour mapper à un fournisseur d’identité. Utilisez l’option « Mappage SAML personnalisées » pour entrer un attribut personnalisé dans la page utilisateurs, ou sélectionnez « E-mail » ou « Identifiant utilisateur » comme attribut de l’utilisateur. Exemple : **E-mail** est sélectionné ici alors que nous mappons la revendication d’identifiant utilisateur avec l’attribut userPrincipalName dans la section **« Attributs utilisateur »** sur le portail Azure. Cela permet de fournir un e-mail utilisateur unique, envoyé à l’application SAP Business Object Cloud pour chaque réponse SAML réussie.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Dans la zone de texte **Informations d’identification de connexion (E-mail)**, entre votre adresse e-mail. Ensuite, cliquez sur le bouton **Vérifier le compte** pour permettre au système d’ajouter vos informations d’identification de connexion à votre compte.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Cliquez sur **l’icône Enregistrer**

    ![Enregistrer](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>Créer un utilisateur de test pour SAP Business Object Cloud

Pour permettre aux utilisateurs Azure AD d’ouvrir une session sur SAP Business Object Cloud, ces derniers doivent être approvisionnés dans SAP Business Object Cloud. Dans le cas de SAP Business Object Cloud, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous en tant qu’administrateur à votre site d’entreprise SAP Business Object Cloud.

2. Accédez à **Menu > Sécurité > Utilisateurs**

    ![Ajouter un employé](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Pour ajouter de nouveaux détails utilisateur, sur la page **Utilisateurs**, cliquez sur **« + »**. Ensuite, réalisez les étapes suivantes :

    ![Inviter des personnes](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. Dans la zone de texte **Identifiant utilisateur**, entrez l’identifiant utilisateur d’un utilisateur, par exemple Britta.

    b. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple Simon.

    d. Dans la zone de texte **Nom d’affichage**, tapez le nom complet d’un utilisateur, par exemple Britta Simon.

    e. Dans la zone de texte **E-mail**, entrez l’adresse e-mail d’utilisateur, par exemple brittasimon@contoso.com.

    f. Sélectionnez le rôle le plus approprié pour l’utilisateur.

      ![Rôle](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. Cliquez sur **l’icône Enregistrer**. 


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Business Object Cloud.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à SAP Business Object Cloud, réalisez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP Business Object Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette SAP Business Object Cloud dans le Panneau d’accès, vous allez en principe être connecté automatiquement à votre application SAP Business Object Cloud.
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


