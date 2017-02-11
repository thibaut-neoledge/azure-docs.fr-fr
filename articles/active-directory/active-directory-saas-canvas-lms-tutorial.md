---
title: "Didacticiel : Intégration d’Azure Active Directory à Canvas LMS | Microsoft Docs"
description: "Apprenez à utiliser Canvas LMS avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0031446f8b5202f6627ed02e90c4789f6d434234


---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Didacticiel : Intégration d’Azure Active Directory à Canvas LMS
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Canvas.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Canvas

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Canvas pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Canvas (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Canvas
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario")

## <a name="enabling-the-application-integration-for-canvas"></a>Activation de l’intégration d’applications pour Canvas
Cette section décrit l’activation de l’intégration d’applications pour Canvas.

### <a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Canvas, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Canvas**.
   
   ![Galerie d’applications](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Canvas**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Canevas](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Canvas avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Canvas vous oblige à récupérer une valeur d’empreinte dans un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Sur la page d’intégration d’application **Canvas** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Canvas ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Canvas**, tapez votre URL selon le modèle `https://<tenant-name>.instructure.com`, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Canvas**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Canvas en tant qu’administrateur.
6. Accédez à **Courses \> Managed Accounts \> Microsoft**.
   
   ![Canevas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
7. Dans le volet de navigation sur la gauche, sélectionnez **Authentication**, puis cliquez sur **Add New SAML Config**.
   
   ![Authentification](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")
8. Dans la page Intégration actuelle, procédez comme suit :
   
   ![Intégration actuelle](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")
   
   1. Dans le portail Azure Classic, sur la page **Configurer l'authentification unique sur Canvas** de la boîte de dialogue, copiez la valeur de **l'ID d'entité**, puis collez-la dans la zone de texte **IdP Entity ID**.
   2. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Canvas**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Log On URL**.
   3. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Canvas**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Log Out URL**.
   4. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Canvas**, copiez la valeur **Modifier l’URL de mot de passe**, puis collez-la dans la zone de texte **Change Password Link**.
   5. Copiez la valeur **Empreinte** du certificat exporté, puis collez-la dans la zone de texte **Certificate Fingerprint**.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Dans la liste **Login Attribute**, sélectionnez **NameID**.
   7. Dans la liste **Identifier Format**, sélectionnez **emailAddress**.
   8. Cliquez sur **Save Authentication Settings**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Canvas, vous devez les approvisionner dans Canvas.  
En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **Canvas** .
2. Accédez à **Courses \> Managed Accounts \> Microsoft**.
   
   ![Canevas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
3. Cliquez sur **Utilisateurs**.
   
   ![Utilisateurs](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")
4. Cliquez sur **Add New User**.
   
   ![Utilisateurs](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")
5. Dans la page Ajouter un nouvel utilisateur, procédez comme suit :
   
   ![Add User](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")
   
   1. Dans la zone de texte **Full Name** , tapez le nom de l’utilisateur.
   2. Dans la zone de texte **Email** , tapez l’adresse de messagerie de l’utilisateur.
   3. Dans la zone de texte **Login** , tapez l’adresse de messagerie Azure AD de l’utilisateur.
   4. Sélectionnez **Email the user about this account creation**.
   5. Cliquez sur **Add User**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Canvas pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Pour affecter des utilisateurs à Canvas, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Canvas**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affectation d’utilisateurs](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


