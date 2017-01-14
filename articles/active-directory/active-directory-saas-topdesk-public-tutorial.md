---
title: "Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - Public | Microsoft Docs"
description: "Découvrez comment utiliser TOPdesk - Public avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: ee413f5044d541a7a127690fab0115341c7619f3


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Didacticiel : Intégration d’Azure Active Directory à TOPdesk - Public
L’objectif de ce didacticiel est de montrer comment intégrer Azure et TOPdesk - Public.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement TOPdesk - Public pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à TOPdesk - Public pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise TOPdesk - Public (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour TOPdesk - Public
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>Activation de l’intégration d’applications pour TOPdesk - Public
Cette section décrit l’activation de l’intégration d’applications pour TOPdesk - Public.

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour TOPdesk - Public, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Add application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Add an application from gallerry")

6. Dans la **zone de recherche**, entrez **TOPdesk - Public**.
   
    ![Galerie d’applications](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Application Gallery")

7. Dans le volet des résultats, sélectionnez **TOPdesk - Public**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur TOPdesk - Public avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour TOPdesk - Public vous oblige à télécharger le fichier d’une icône. Pour obtenir ce fichier d’icône, contactez l’équipe de support TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **TOPdesk - Public** en tant qu’administrateur.

2. Dans le menu **TOPdesk**, cliquez sur **Settings**.
   
    ![Paramètres](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")

3. Cliquez sur **Login Settings**.
   
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

4. Développez le menu **Login Settings**, puis cliquez sur **General**.
   
    ![Généralités](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5. Dans la section **Public** de la section de configuration **SAML login**, procédez comme suit :
   
    ![Technical Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")
   
    a. Cliquez sur **Download** pour télécharger le fichier de métadonnées public et enregistrez-le en local sur votre ordinateur.
   
    b. Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService**.
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   
    c. Copiez la valeur de **AssertionConsumerService** .  
      
    > [!NOTE]
    > Vous en aurez besoin dans la section **Configurer l’URL de l’application** plus loin dans ce didacticiel.
    > 
    > 

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail Azure Classic** en tant qu’administrateur.

7. Dans la page d’intégration d’application **TOPdesk - Public**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configure Single Sign-On")

8. Dans la page **Comment voulez-vous que les utilisateurs se connectent à TOPdesk - Public**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configure Single Sign-On")

9. Dans la page **Configurer l’URL de l’application** , procédez comme suit :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configure App URL")
   
    a. Dans la zone de texte **URL d’authentification de TOPdesk - Public**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application TOPdesk - Public (par exemple : *https://qssolutions.topdesk.net*).
   
    b. Dans la zone de texte **URL de réponse TOPdesk - Public**, collez l’**URL d’AssertionConsumerService TOPdesk - Public** (par exemple : *https://qssolutions.topdesk.net/tas/public/login/saml*).
   
    c. Cliquez sur **Next**.

10. Dans la page **Configurer l’authentification unique sur TOPdesk - Public**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier localement sur votre ordinateur.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configure Single Sign-On")

11. Pour créer un fichier de certificat, procédez comme suit :
    
    ![Certificat](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")
    
    1. Ouvrez le fichier de métadonnées téléchargé.
    2. Développez le nœud **RoleDescriptor** dont le **xsi:type** est **fed:ApplicationServiceType**.
    3. Copiez la valeur du nœud **X509Certificate** .
    4. Enregistrez la valeur de **X509Certificate** copiée, dans un fichier local sur votre ordinateur.
12. Dans le menu **TOPdesk** de votre site d’entreprise TOPdesk - Public, cliquez sur **Settings**.
    
    ![Paramètres](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")
13. Cliquez sur **Login Settings**.
    
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
14. Développez le menu **Login Settings**, puis cliquez sur **General**.
    
    ![Généralités](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
15. Dans la section **Public**, cliquez sur **Add**.
    
    ![SAML login](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML Login")
16. Dans la boîte de dialogue **SAML configuration assistant** , procédez comme suit :
    
    ![SAML configuration assistant](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")
    
    a. Pour charger votre fichier de métadonnées téléchargé, dans **Federation Metadata**, cliquez sur **Browse**.

    b. Pour charger votre fichier de certificat, sous **Certificate (RSA)**, cliquez sur **Browse**.

    c. Pour charger le fichier de logo que vous avez obtenu de l’équipe de support TOPdesk, sous **Logo icon**, cliquez sur **Browse**.

    d. Dans la zone de texte **User name attribute**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Dans la zone de texte **Display name** , indiquez le nom de votre configuration.

    f. Cliquez sur **Enregistrer**.

17. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à TOPdesk - Public, les utilisateurs d’Azure AD doivent être approvisionnés dans TOPdesk - Public.  
Dans le cas de TOPdesk - Public, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **TOPdesk - Public** en tant qu’administrateur.

2. Dans le menu en haut, cliquez sur **TOPdesk \> New \> Support Files \> Person**.
   
    ![Personne](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3. Dans la boîte de dialogue New User, procédez comme suit :
   
    ![New Person](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")
   
    a. Cliquez sur l’onglet General.

    b. Dans la zone de texte Surname, indiquez le nom du compte Azure AD valide que vous souhaitez approvisionner.
 
    c. Sélectionnez un **Site** pour le compte.
 
    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par TOPdesk - Public, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Pour affecter des utilisateurs à TOPdesk - Public, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **TOPdesk - Public**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assign Users")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


