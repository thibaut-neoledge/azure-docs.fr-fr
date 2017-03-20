---
title: "Didacticiel : Intégration d’Azure Active Directory à TOPdesk - Secure | Microsoft Docs"
description: "Découvrez comment utiliser TOPdesk - Secure avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Didacticiel : Intégration d’Azure Active Directory à TOPdesk - Secure
L’objectif de ce didacticiel est de montrer comment intégrer Azure et TOPdesk - Secure.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement TOPdesk - Secure pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à TOPdesk - Secure pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise TOPdesk - Secure (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour TOPdesk - Secure
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scénario")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Activation de l’intégration d’applications pour TOPdesk - Secure
Cette section décrit l’activation de l’intégration d’applications pour TOPdesk - Secure.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour TOPdesk - Secure, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **TOPdesk - Secure**.
   
    ![Galerie d’applications](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galerie d’applications")

7. Dans le volet des résultats, sélectionnez **TOPdesk - Secure**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur TOPdesk - Secure avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour TOPdesk - Secure vous oblige à télécharger le fichier d’une icône. Pour obtenir ce fichier d’icône, contactez l’équipe de support TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **TOPdesk - Secure** en tant qu’administrateur.
2. Dans le menu **TOPdesk**, cliquez sur **Settings**.
   
    ![Paramètres](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Paramètres")

3. Cliquez sur **Login Settings**.
   
    ![Paramètres de connexion](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Paramètres de connexion")

4. Développez le menu **Login Settings**, puis cliquez sur **General**.
   
    ![Général](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Général")

5. Dans la section **Secure** de la section de configuration **SAML login**, procédez comme suit :
   
    ![Paramètres techniques](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Paramètres techniques")
   
    a. Cliquez sur **Download** pour télécharger le fichier de métadonnées public et enregistrez-le en local sur votre ordinateur.
   
    b. Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService**.
    
    ![Assertion Consumer Service](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Assertion Consumer Service")
   
    c. Copiez la valeur de **AssertionConsumerService** .  
      
    > [!NOTE]
    > Vous en aurez besoin dans la section **Configurer l’URL de l’application** plus loin dans ce didacticiel.
    > 
    > 

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail Azure Classic** en tant qu’administrateur.

7. Dans la page d’intégration d’application **TOPdesk - Secure**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurer l’authentification unique")

8. Dans la page **Comment voulez-vous que les utilisateurs se connectent à TOPdesk - Secure**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurer l’authentification unique")

9. Dans la page **Configurer l’URL de l’application** , procédez comme suit :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurer l’URL de l’application")
   
    a. Dans la zone de texte **URL d’authentification de TOPdesk - Secure**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application TOPdesk - Secure (par exemple : *https://qssolutions.topdesk.net*).
   
    b. Dans la zone de texte **URL de réponse TOPdesk - Secure**, collez l’**URL d’AssertionConsumerService TOPdesk - Secure** (par exemple : *https://qssolutions.topdesk.net/tas/public/login/saml*).
   
    c. Cliquez sur **Suivant**.

10. Dans la page **Configurer l’authentification unique sur TOPdesk - Secure**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier localement sur votre ordinateur.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurer l’authentification unique")

11. Pour créer un fichier de certificat, procédez comme suit :
    
    ![Certificat](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificat")
    
    a. Ouvrez le fichier de métadonnées téléchargé.
    b. Développez le nœud **RoleDescriptor** dont le **xsi:type** est **fed:ApplicationServiceType**.
    c. Copiez la valeur du nœud **X509Certificate** .
    d. Enregistrez la valeur de **X509Certificate** copiée, dans un fichier local sur votre ordinateur.

12. Dans le menu **TOPdesk** de votre site d’entreprise TOPdesk - Secure, cliquez sur **Settings**.
    
    ![Paramètres](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Paramètres")

13. Cliquez sur **Login Settings**.
    
    ![Paramètres de connexion](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Paramètres de connexion")

14. Développez le menu **Login Settings**, puis cliquez sur **General**.
    
    ![Général](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Général")

15. Dans la section **Public**, cliquez sur **Add**.
    
    ![Add](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Add")

16. Dans la boîte de dialogue **SAML configuration assistant** , procédez comme suit :
    
    ![Assistant de configuration SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistant de configuration SAML")
    
    a. Pour charger votre fichier de métadonnées téléchargé, dans **Federation Metadata**, cliquez sur **Browse**.

    b. Pour charger votre fichier de certificat, sous **Certificate (RSA)**, cliquez sur **Browse**.

    c. Pour charger le fichier de logo que vous avez obtenu de l’équipe de support TOPdesk, sous **Logo icon**, cliquez sur **Browse**.

    d. Dans la zone de texte **User name attribute**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Dans la zone de texte **Display name** , indiquez le nom de votre configuration.

    f. Cliquez sur **Enregistrer**.

17. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à TOPdesk - Secure, les utilisateurs d’Azure AD doivent être approvisionnés dans TOPdesk - Secure.  
Dans le cas de TOPdesk - Secure, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **TOPdesk - Secure** en tant qu’administrateur.
2. Dans le menu en haut, cliquez sur **TOPdesk \> New \> Support Files \> Operator**.
   
    ![Operator (Opérateur)](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operator (Opérateur)")

3. Dans la boîte de dialogue **New Operator** , procédez comme suit :
   
    ![New Operator (Nouvel opérateur)](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "New Operator (Nouvel opérateur)")
   
    a. Cliquez sur l’onglet General.
   
    b. Dans la zone de texte **Surname** de la section **General**, indiquez le nom du compte Azure Active Directory valide que vous souhaitez approvisionner.
   
    c. Sélectionnez un **Site** pour le compte dans la section **Emplacement**.
   
    d. Dans la zone de texte **Login Name** de la section **TOPdesk Login**, indiquez le nom de connexion de votre utilisateur.
   
    e. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par TOPdesk - Secure, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Pour affecter des utilisateurs à TOPdesk - Secure, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **TOPdesk - Secure**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


