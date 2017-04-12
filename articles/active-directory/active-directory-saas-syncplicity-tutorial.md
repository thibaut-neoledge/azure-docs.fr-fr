---
title: "Didacticiel : Intégration d’Azure Active Directory à Syncplicity | Microsoft Docs"
description: "Découvrez comment utiliser Syncplicity avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 044769ca5a74b90843cb29510b66d9ce7e90566c
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Didacticiel : Intégration d’Azure Active Directory à Syncplicity
L’objectif de ce didacticiel est de montrer comment configurer l’authentification unique entre Azure Active Directory (Azure AD) et Syncplicity.

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Syncplicity

À l’issue de ce didacticiel, les utilisateurs d’Azure AD auxquels vous avez affecté un accès à Syncplicity pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Syncplicity (connexion initiée par le fournisseur du service) ou à l’aide de la Présentation du panneau d’accès Azure AD.

1. Activation de l’intégration d’applications pour Syncplicity
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scénario")

## <a name="enable-the-application-integration-for-syncplicity"></a>Activer l’intégration d’applications pour Syncplicity
Cette section décrit l’activation de l’intégration d’applications pour Syncplicity.

**Pour activer l’intégration d’applications pour Syncplicity, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **Syncplicity**.
   
    ![Galerie d’applications Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Galerie d’applications Syncplicity")

7. Dans le volet des résultats, sélectionnez **Syncplicity**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Syncplicity avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans la page d’intégration d’application **Syncplicity** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Syncplicity**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Authentification unique avec Microsoft Azure AD](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Authentification unique avec Microsoft Azure AD")

3. Dans la zone de texte **URL de connexion à Syncplicity** de la page **Configurer l’URL de l’application**, entrez l’URL que les utilisateurs doivent taper pour se connecter à votre application Syncplicity, puis cliquez sur **Suivant**. 
   
    L’URL de l’application est celle de votre locataire Syncplicity (par exemple, *http://company.Syncplicity.com*) :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur Syncplicity**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configurer l’authentification unique")

5. Connectez-vous à votre locataire **Syncplicity** .

6. Dans le menu en haut, cliquez sur **admin**, sélectionnez **settings**, puis cliquez sur **Custom domain and single sign-on**.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7. Dans la boîte de dialogue **Single Sign on (SSO)** , procédez comme suit :
   
    ![Authentification unique \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \\\(SSO\\\)")   
  1. Dans la zone de texte **Custom Domain** , entrez le nom de votre domaine.
  2. Sélectionnez **Enabled** dans **Single Sign-On Status**.
  3. Dans la page **Configurer l’authentification unique sur Syncplicity** du portail Azure Classic, copiez la valeur de **ID de l’entité** et collez-la dans la zone de texte **Entity Id**.
  4. Dans la page **Configurer l’authentification unique sur Syncplicity** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Sign-in page URL**.
  5. Dans la page **Configurer l’authentification unique sur Syncplicity** du portail Azure Classic, copiez la valeur de **URL de déconnexion distante** et collez-la dans la zone de texte **Logout page URL**.
  6. Dans **Certificat du fournisseur d’identité**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure Classic. 
  7. Cliquez sur **Enregistrer les modifications**.

8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Confirmation](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmation")

## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur
Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Syncplicity. Cette section décrit comment créer des comptes d’utilisateur AAD dans Syncplicity.

**Pour approvisionner un compte d’utilisateur dans Syncplicity, procédez comme suit :**

1. Connectez-vous à votre locataire **Syncplicity** (par exemple, *https://company.Syncplicity.com*).

2. Cliquez sur **Admin** et sélectionnez **user accounts**.

3. Cliquez sur **Add a user**.
   
    ![Gestion des utilisateurs](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Gestion des utilisateurs")

4. Entrez l’adresse e-mail d’un compte AAD valide que vous souhaitez approvisionner dans la zone de texte **Email address**, sélectionnez **User** dans **Role**, puis cliquez sur **Next**.
   
    ![Informations du compte](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Informations du compte")
   
    >[!NOTE]
    >Le détenteur du compte AAD reçoit un message électronique contenant un lien pour confirmer et activer le compte. 
    > 

5. Sélectionnez un groupe dans votre société dont votre nouvel utilisateur doit devenir membre, puis cliquez sur **Next**.
   
    ![Appartenance de groupe](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Appartenance de groupe")
   
    >[!NOTE]
    >Si aucun groupe n’est répertorié, il suffit de cliquer sur **Next**. 
    > 

6. Sélectionnez les dossiers que vous souhaitez placer sous le contrôle de Syncplicity sur l’ordinateur de l’utilisateur, puis cliquez sur **Next**.
   
    ![Dossiers Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Dossiers Syncplicity")

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Syncplicity, pour approvisionner des comptes utilisateur AAD. 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Syncplicity, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **Syncplicity**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


