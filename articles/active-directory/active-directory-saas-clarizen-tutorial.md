---
title: "Didacticiel : Intégration d’Azure Active Directory à Clarizen | Microsoft Docs"
description: "Apprenez à utiliser Clarizen avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f8d2418fe86163a4022960f87803c82487c86fd


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Didacticiel : Intégration d’Azure Active Directory à Clarizen
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Clarizen.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Clarizen pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Clarizen pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Clarizen (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Clarizen
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## <a name="enabling-the-application-integration-for-clarizen"></a>Activation de l’intégration d’applications pour Clarizen
Cette section décrit l’activation de l’intégration d’applications pour Clarizen.

### <a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Clarizen, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Clarizen**.
   
   ![Galerie d’applications](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Clarizen**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Clarizen avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’applications **Clarizen**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Clarizen**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")
3. Dans la page **Configurer l’authentification unique sur Clarizen**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")
4. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Clarizen** en tant qu’administrateur (par exemple, *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).
5. Cliquez sur votre nom d’utilisateur, puis sur **Settings**.
   
   ![Settings](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")
6. Cliquez sur l’onglet **Global Settings**, puis cliquez sur **edit** en regard de **Federated Authentication**.
   
   ![Paramètres globaux](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. Dans la boîte de dialogue **Federated Authentication** , procédez comme suit :
   
   ![Federated Authentication](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. Cliquez sur **Upload** pour charger votre certificat téléchargé.
   2. Dans la page **Configurer l’authentification unique sur Clarizen** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Sign-in URL**.
   3. Dans la page **Configurer la déconnexion unique sur Clarizen** du portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Sign-out URL**.
   4. Sélectionnez **Use POST**.
   5. Cliquez sur **Save**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Clarizen, les utilisateurs d’Azure AD doivent être approvisionnés dans Clarizen.  
Dans le cas de Clarizen, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous au site d’entreprise **Clarizen** en tant qu’administrateur.
2. Cliquez sur **People**.
   
   ![Personnes](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. Cliquez sur **Invite User**.
   
   ![Inviter des utilisateurs](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. Dans la boîte de dialogue Invite People, procédez comme suit :
   
   ![Inviter des personnes](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. Dans la zone de texte **Email** , tapez l’adresse électronique d’un compte Azure Active Directory valide à approvisionner.
   2. Cliquez sur **Invite**.
   
   > [!NOTE]
   > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.
   > 
   > 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Pour affecter des utilisateurs à Clarizen, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Clarizen**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


