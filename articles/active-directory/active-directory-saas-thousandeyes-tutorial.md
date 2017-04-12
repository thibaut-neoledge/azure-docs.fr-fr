---
title: "Didacticiel : intégration d’Azure Active Directory à ThousandEyes | Microsoft Docs"
description: "Découvrez comment utiliser ThousandEyes avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 7bc96e6a711c70f9c5fa5daa4e059d9d7c04a134
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Didacticiel : Intégration d’Azure AD à ThousandEyes
L’objectif de ce didacticiel est de montrer comment configurer l’authentification unique entre Azure Active Directory (Azure AD) et ThousandEyes.

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement ThousandEyes pour lequel l’authentification unique (SSO) est activée

À l’issue de ce didacticiel, les utilisateurs d’AAD auxquels vous avez affecté un accès à ThousandEyes pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ThousandEyes (connexion initiée par le fournisseur du service) ou à l’aide du panneau d’accès AAD.

1. Activation de l’intégration d’applications pour ThousandEyes
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scénario")

## <a name="enable-the-application-integration-for-thousandeyes"></a>Activer l’intégration d’applications pour ThousandEyes
Cette section décrit l’activation de l’intégration d’application pour ThousandEyes.

**Pour activer l’intégration d’applications pour ThousandEyes, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **ThousandEyes**.
   
    ![Galerie d’applications](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **ThousandEyes**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ThousandEyes avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans la page d’intégration d’application **ThousandEyes** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ThousandEyes**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configurer l’authentification unique")

3. Dans la zone de texte **URL de connexion à ThousandEyes** de la page **Configurer l’URL de l’application**, entrez l’URL que les utilisateurs doivent entrer pour se connecter à votre application ThousandEyes (par ex., *https://app.thousandeyes.com/login/sso*), puis cliquez sur **Suivant**. 
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur ThousandEyes**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **ThousandEyes** en tant qu’administrateur.

6. Dans le menu situé en haut, cliquez sur **Settings**.
   
    ![Paramètres](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Paramètres")

7. Cliquez sur **Account**
   
    ![Compte](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Compte")

8. Cliquez sur l’onglet **Security & Authentication (Sécurité et authentification)**.
   
    ![Sécurité et authentification](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Sécurité et authentification")

9. Dans la section **Setup Single Sign-On** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Configurer l’authentification unique")
  1. Sélectionnez **Activer l'authentification unique**.
  2. Dans la page **Configurer l’authentification unique sur ThousandEyes** du portail Microsoft Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Login Page URL (URL de la page de connexion)**.
  3. Dans la page **Configurer l’authentification unique sur ThousandEyes** du portail Microsoft Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout Page URL (URL de la page de déconnexion)**.
  4. Dans la page **Configurer l’authentification unique sur ThousandEyes** du portail Microsoft Azure Classic, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **Émetteur du fournisseur d’identité**.
  5. Dans **Certificat du fournisseur d’identité**, cliquez sur **Choisir un fichier**, puis chargez le certificat que vous avez téléchargé à partir du portail Microsoft Azure Classic.
  6. Cliquez sur **Enregistrer**.

10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configurer l’authentification unique")

## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur
Pour se connecter à ThousandEyes, les utilisateurs d’Azure AD doivent être approvisionnés dans ThousandEyes.  
Dans le cas de ThousandEyes, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur dans ThousandEyes, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise ThousandEyes en tant qu’administrateur.

2. Cliquez sur **Settings**.
   
    ![Paramètres](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Paramètres")

3. Cliquez sur **Account**.
   
    ![Compte](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Compte")

4. Cliquez sur l’onglet **Accounts & Users (Comptes et utilisateurs)**.
   
    ![Comptes et utilisateurs](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Comptes et utilisateurs")

5. Dans la section **Add Users & Accounts (Ajouter des utilisateurs et des comptes)**, procédez comme suit :
   
    ![Ajouter des comptes d’utilisateurs](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Ajouter des comptes d’utilisateurs")   
  1. Indiquez le **nom**, **l’adresse e-mail** et d’autres détails du compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
  2. Cliquez sur le bouton **Add New User to Account**.
      
     >[!NOTE]
     >Le détenteur du compte AAD reçoit un message électronique contenant un lien pour confirmer et activer le compte.
     >  

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par ThousandEyes, pour approvisionner des comptes d’utilisateur AAD.
>  

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à ThousandEyes, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **ThousandEyes**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


