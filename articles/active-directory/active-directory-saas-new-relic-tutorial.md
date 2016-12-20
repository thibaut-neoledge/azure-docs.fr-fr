---
title: "Didacticiel : Intégration d’Azure Active Directory à New Relic | Microsoft Docs"
description: "Apprenez à utiliser New Relic avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7c7542454bab5e5c2fae88aeefa1a981ee165ff


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Didacticiel : Intégration d’Azure Active Directory à New Relic
L’objectif de ce didacticiel est de montrer comment configurer l’authentification unique entre Azure Active Directory et New Relic.

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement New Relic pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure Active Directory que vous avez affectés à New Relic pourront s’authentifier de manière unique à l’aide du panneau d’accès AAD.

1. Activation de l’intégration d’application pour New Relic
2. Configuration de l’authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")

## <a name="enabling-the-application-integration-for-new-relic"></a>Activation de l’intégration d’application pour New Relic
Cette section décrit l’activation de l’intégration d’application pour New Relic.

### <a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Pour activer l’intégration d’application pour New Relic, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **New Relic**.
   
   ![Galerie d’applications](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **New Relic**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur New Relic avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **New Relic**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à New Relic ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")
3. Dans la zone de texte **URL de connexion de New Relic** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application New Relic, puis cliquez sur **Suivant**. 
   
   L’URL de l’application est votre locataire New Relic (par exemple, *https://rpm.newrelic.com*):
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur New Relic**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **New Relic** en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Account Settings**.
   
   ![Account Settings](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")
7. Cliquez sur l’onglet **Security and authentication**, puis sur l’onglet **Single sign on**.
   
   ![Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")
8. Dans la page de boîte de dialogue SAML, procédez comme suit :
   
   ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")
   
   1. Pour charger le certificat Azure Active Directory téléchargé, cliquez sur **Choose File** .
   2. Dans la page **Configurer l’authentification unique sur New Relic** du portail Azure Classic, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Remote login URL**.
   3. Dans la page **Configurer l’authentification unique sur New Relic** du portail Azure Classic, copiez la valeur de **URL de déconnexion distante** et collez-la dans la zone de texte **Logout landing URL**.
   4. Cliquez sur **Save my changes**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à New Relic, les utilisateurs d’Azure AD doivent être approvisionnés dans New Relic.  
Dans le cas de New Relic, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur dans New Relic, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **New Relic** en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur **Account Settings**.
   
   ![Account Settings](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")
3. Dans le volet **Account** situé sur le côté gauche, cliquez sur **Summary**, puis sur **Add user**.
   
   ![Account Settings](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")
4. Dans la boîte de dialogue **Active users** , procédez comme suit :
   
   ![Active users](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")
   
   1. Dans la zone de texte **Email** , tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide à approvisionner.
   2. Pour **Role**, sélectionnez **User**.
   3. Cliquez sur **Add this user**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par New Relic, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Pour affecter des utilisateurs à New Relic, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **New Relic**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


