---
title: "Didacticiel : Intégration d’Azure Active Directory à ServiceNow | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Service Now et ServiceNow Express."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Didacticiel : Intégration d’Azure Active Directory à ServiceNow
Dans ce didacticiel, vous allez apprendre à intégrer ServiceNow et ServiceNow Express à Azure Active Directory (Azure AD).

L’intégration de ServiceNow et ServiceNow Express à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à ServiceNow et ServiceNow Express.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ServiceNow et ServiceNow Express (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables
Pour configurer l’intégration d’Azure AD à ServiceNow et ServiceNow Express, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Pour ServiceNow, une instance ou un locataire ServiceNow, version Calgary ou supérieure
* Pour ServiceNow Express, une instance ServiceNow Express, version Helsinki ou supérieure
* Le locataire ServiceNow doit avoir le [plug-in d’authentification unique à plusieurs fournisseurs](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) activé. Cette opération est possible en [envoyant une demande de service](https://hi.service-now.com). 

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ServiceNow à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD pour ServiceNow ou ServiceNow Express

## <a name="adding-servicenow-from-the-gallery"></a>Ajout de ServiceNow à partir de la galerie
Pour configurer l’intégration de ServiceNow ou ServiceNow Express à Azure AD, vous devez ajouter Service Now depuis la galerie à votre liste d’applications SaaS gérées. 

**Pour ajouter ServiceNow à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **ServiceNow**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. Dans le volet des résultats, sélectionnez **ServiceNow**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ServiceNow ou ServiceNow Express avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ServiceNow équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur ServiceNow associé doit être établie.
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans ServiceNow. Pour configurer et tester l’authentification unique Azure AD avec ServiceNow, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD pour ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configuration de l’authentification unique Azure AD pour ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
3. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test ServiceNow](#creating-a-servicenow-test-user)** pour avoir un équivalent de Britta Simon dans ServiceNow lié à sa représentation dans Azure AD.
5. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

> [!NOTE]
> Si vous voulez configurer ServiceNow, omettez l’étape 2. De même, si vous voulez configurer ServiceNow Express, omettez l’étape 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuration de l’authentification unique Azure AD pour ServiceNow
1. Dans la page d’intégration d’applications **ServiceNow** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ServiceNow**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurer l’authentification unique")

3. Dans la page **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer l’URL de l’application")
   
    a. Dans la zone de texte **URL d’authentification unique ServiceNow**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ServiceNow : `https://<instance-name>.service-now.com`.
   
    b. Dans la zone de texte **Identificateur**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ServiceNow : `https://<instance-name>.service-now.com`.
   
    c. Cliquez sur **Suivant**

4. Pour permettre à Azure AD de configurer automatiquement ServiceNow pour l’authentification basée SAML, entrez votre nom d’instance ServiceNow, le nom d’utilisateur administrateur et le mot de passe administrateur dans le formulaire **Configurer automatiquement l’authentification unique** puis cliquez sur *Configurer*. Notez que le nom d’utilisateur administrateur fourni doit avoir le rôle **security_admin** attribué dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow afin d’utiliser Azure AD comme fournisseur d’identité SAML, cliquez sur **Configurer manuellement l’application pour l’authentification unique**, sur **Suivant**, puis effectuez les étapes suivantes.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer l’URL de l’application")

5. Dans la page **Configurer l’authentification unique à ServiceNow**, cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat en local sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurer l’authentification unique")

6. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

7. Activez le plug-in *Integration - Multiple Provider Single Sign-On Installer* (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs) en suivant la procédure ci-dessous :
   
    a. Dans le volet de navigation à gauche, accédez à la section **System Definition** (Définition du système), puis cliquez sur **Plugins** (Plug-ins).
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Activer le plug-in")
   
    b. Recherchez *Integration - Multiple Provider Single Sign-On Installer* (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs).
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Activer le plug-in")
   
    c. Sélectionnez le plug-in. Cliquez avec le bouton droit et sélectionnez **Activate/Upgrade** (Activer/Mettre à niveau).
   
    d. Cliquez sur le bouton **Activate** (Activer).

8. À gauche du volet de navigation, cliquez sur **Properties**.  
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurer l’URL de l’application")

9. Dans la boîte de dialogue **Multiple Provider SSO Properties** , effectuez les opérations suivantes :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurer l’URL de l’application")
   
    a. Pour **Enable multiple provider SSO**, sélectionnez **Yes**.
   
    b. Pour **Enable debug logging got the multiple provider SSO integration**, sélectionnez **Yes**.
   
    c. Dans la zone de texte **The field on the user table that...**, entrez **user_name**.
   
    d. Cliquez sur **Enregistrer**.

10. À gauche du volet de navigation, cliquez sur **x509 Certificates**.
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurer l’authentification unique")

11. Dans la boîte de dialogue **X.509 Certificates**, cliquez sur **New**.
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurer l’authentification unique")

12. Dans la boîte de dialogue **X.509 Certificates** , procédez comme suit :
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurer l’authentification unique")
    
     a. Cliquez sur **Nouveau**.
    
     b. Dans la zone de texte **Name**, indiquez le nom de votre configuration (p. ex., **TestSAML2.0**).
    
     c. Sélectionnez **Active**.
    
     d. Pour **Format**, sélectionnez **PEM**.
    
     e. Pour **Type**, sélectionnez **Trust Store Cert**.
    
     f. Ouvrez votre certificat codé en base64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PEM Certificate** (Certificat PEM).
    
     g. Cliquez sur **Update**.

13. À gauche du volet de navigation, cliquez sur **Identity Providers**.
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

14. Dans la boîte de dialogue **Identity Providers**, cliquez sur **New** :
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurer l’authentification unique")

15. Dans la boîte de dialogue **Identity Providers**, cliquez sur **SAML2 Update1?** :
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurer l’authentification unique")

16. Dans la boîte de dialogue SAML2 Update1 Properties, effectuez les opérations suivantes :
    
     ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Nom**, tapez le nom de votre configuration (ex. **SAML 2.0**).

    b. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow. 

    > [!NOTE] 
    > Vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse de messagerie comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attributs > Authentification unique) du portail Azure Classic et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ saisi (par exemple, user_name)

    c. Dans le portail Azure AD Classic, copiez la valeur de **l’ID de fournisseur d’identité**, puis collez-la dans la zone de texte **URL de fournisseur d’identité**.

    d. Dans le portail Azure AD Classic, copiez la valeur de **l’URL de la demande d’authentification**, puis collez-la dans la zone de texte **Demande d’authentification du fournisseur d’identité**.

    e. Dans le portail Azure AD Classic, copiez la valeur de **l’URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Demande de déconnexion unique du fournisseur d’identité**.

    f. Dans la zone de texte **Page d’accueil ServiceNow** , entrez l’URL de la page d’accueil de votre instance ServiceNow.

    > [!NOTE] 
    > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (p. ex., `https://fabrikam.service-now.com/navpage.do`).

    g. Dans la zone de texte **ID de l’entité / Émetteur** , entrez l’URL de votre locataire ServiceNow.

    h. Dans la zone de texte **URL de l’audience** , entrez l’URL de votre locataire ServiceNow. 

    i. Dans la zone de texte **Liaison du protocole pour la demande de déconnexion unique du fournisseur d’identité**, entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. Dans la zone de texte Stratégie d’ID de nom, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Désélectionnez **Créer une classe de contexte d’authentification**.

    l. Dans **AuthnContextClassRef Method**, entrez `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Cela n’est nécessaire que si votre organisation utilise uniquement le cloud. Si vous utilisez des services ADFS ou MFA locaux pour l’authentification, vous ne devez pas configurer cette valeur. 

    m. Dans la zone de texte **Variation d’horloge**, entrez **60**.

    n. Pour **Script d’authentification unique**, sélectionnez **MultiSSO_SAML2_Update1**.

    o. Pour **Certificat x509**, sélectionnez le certificat que vous avez créé à l’étape précédente.

    p. Cliquez sur **Envoyer**. 

1. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurer l’authentification unique")

2. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurer l’authentification unique")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuration de l’authentification unique Azure AD pour ServiceNow Express
1. Dans la page d’intégration d’applications **ServiceNow** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ServiceNow**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurer l’authentification unique")

3. Dans la page **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer l’URL de l’application")
   
    a. Dans la zone de texte **URL d’authentification unique ServiceNow**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ServiceNow : `https://<instance-name>.service-now.com`.
   
    b. Dans la zone de texte **URL de l’émetteur**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ServiceNow `https://<instance-name>.service-now.com`.
   
    c. Cliquez sur **Suivant**

4. Cliquez sur **Configurer manuellement l'authentification unique pour cette application**, puis cliquez sur **Suivant** et suivez la procédure ci-dessous.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer l’URL de l’application")

5. Dans la page **Configurer l’authentification unique à ServiceNow**, cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat en local sur votre ordinateur, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurer l’authentification unique")

6. Connectez-vous à votre application ServiceNow Express en tant qu’administrateur.

7. Dans le volet de navigation à gauche, cliquez sur **Authentification unique**.  
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurer l’URL de l’application")

8. Dans la boîte de dialogue **Authentification unique**, cliquez sur l’icône de configuration en haut à droite et définissez les propriétés suivantes :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurer l’URL de l’application")
   
    a. Activez **Enable multiple provider SSO** (Activer l’authentification unique à plusieurs fournisseurs) à droite.
   
    b. Activez **Enable debug logging for the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique à plusieurs fournisseurs) à droite.
   
    c. Dans la zone de texte **The field on the user table that...**, entrez **user_name**.
9. Dans la boîte de dialogue **Authentification unique**, cliquez sur **Add New Certificate** (Ajouter un nouveau certificat).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurer l’authentification unique")
10. Dans la boîte de dialogue **X.509 Certificates** , procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurer l’authentification unique")
    
    a. Dans la zone de texte **Name**, indiquez le nom de votre configuration (p. ex., **TestSAML2.0**).
    
    b. Sélectionnez **Active**.
    
    c. Pour **Format**, sélectionnez **PEM**.
    
    d. Pour **Type**, sélectionnez **Trust Store Cert**.
    
    e. Créez un fichier codé en base64 à partir du certificat téléchargé.
    
    > [!NOTE]
    > Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. Ouvrez votre certificat codé en base64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PEM Certificate** (Certificat PEM).
    
    g. Cliquez sur **Update**.
11. Dans la boîte de dialogue **Authentification unique**, cliquez sur **Add New IdP** (Ajouter un nouveau fournisseur d’identité).
    
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurer l’authentification unique")
12. Dans la boîte de dialogue **Add New Identity Provider** (Ajouter un nouveau fournisseur d’identité), sous **Configure Identity Provider** (Configurer un fournisseur d’identité), procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Nom**, tapez le nom de votre configuration (par ex., **SAML 2.0**).

    b. Dans le portail Azure AD Classic, copiez la valeur de **l’ID de fournisseur d’identité**, puis collez-la dans la zone de texte **URL de fournisseur d’identité**.

    c. Dans le portail Azure AD Classic, copiez la valeur de **l’URL de la demande d’authentification**, puis collez-la dans la zone de texte **Demande d’authentification du fournisseur d’identité**.

    d. Dans le portail Azure AD Classic, copiez la valeur de **l’URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Demande de déconnexion unique du fournisseur d’identité**.

    e. Pour **Identity Provider Certificate** (Certificat du fournisseur d’identité), sélectionnez le certificat que vous avez créé à l’étape précédente.


1. Cliquez sur **Advanced Settings** (Paramètres avancés), et sous **Additional Identity Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurer l’authentification unique")
   
    a. Dans la zone de texte **Liaison du protocole pour la demande de déconnexion unique du fournisseur d’identité**, entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.
   
    b. Dans la zone de texte **Stratégie d’ID de nom**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.    
   
    c. Dans la **méthode AuthnContextClassRef**, saisissez **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Désélectionnez **Créer une classe de contexte d’authentification**.

2. Sous **Additional Service Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurer l’authentification unique")
   
    a. Dans la zone de texte **Page d’accueil ServiceNow** , entrez l’URL de la page d’accueil de votre instance ServiceNow.
   
    > [!NOTE]
    > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (p. ex., `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. Dans la zone de texte **ID de l’entité / Émetteur** , entrez l’URL de votre locataire ServiceNow.
   
    c. Dans la zone de texte **URI d’audience** , entrez l’URL de votre locataire ServiceNow. 
   
    d. Dans la zone de texte **Variation d’horloge**, entrez **60**.
   
    e. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.
   
    > [!NOTE]
    > Vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse de messagerie comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attributs > Authentification unique) du portail Azure Classic et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ saisi (par exemple, user_name)
    > 
    > 
   
    f. Cliquez sur **Enregistrer**. 

3. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurer l’authentification unique")

4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Dans la page d’intégration d’applications **ServiceNow** du portail de gestion Azure Classic, cliquez sur **Configurer l’approvisionnement d’utilisateurs**. 
   
    ![Approvisionnement d'utilisateurs](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Approvisionnement d’utilisateurs")

2. Dans la page **Entrez vos informations d’identification ServiceNow pour activer la configuration automatique d’un utilisateur**, indiquez les paramètres de configuration suivants :
   
     a. Dans la zone de texte **Nom de l'Instance ServiceNow** , tapez le nom d'instance ServiceNow.
   
     b. Dans la zone de texte **Nom d’utilisateur admin ServiceNow** , entrez le nom du compte d’administrateur ServiceNow.
   
     c. Dans la zone de texte **Mot de passe de l’admin ServiceNow** , entrez le mot de passe de ce compte.
   
     d. Cliquez sur **Valider** pour vérifier votre configuration.
   
     e. Cliquez sur le bouton **Suivant** pour ouvrir la page **Étapes suivantes**.
   
     f. Si vous voulez approvisionner tous les utilisateurs pour cette application, sélectionnez «**Approvisionner automatiquement tous les comptes du répertoire dans cette application**». 
   
    ![Étapes suivantes](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Étapes suivantes")
   
     g. Sur la page **Étapes suivantes**, cliquez sur **Terminer** pour enregistrer votre configuration.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Suivant**.

6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Suivant**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-servicenow-test-user"></a>Création d’un utilisateur de test ServiceNow
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans ServiceNow. Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans ServiceNow. Si vous ne savez pas comment ajouter un utilisateur dans votre compte ServiceNow ou ServiceNow Express, contactez l’équipe de support technique de ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ServiceNow.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à ServiceNow, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ServiceNow**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 

4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette ServiceNow dans le volet d’accès, vous devez être connecté automatiquement à votre application ServiceNow.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
