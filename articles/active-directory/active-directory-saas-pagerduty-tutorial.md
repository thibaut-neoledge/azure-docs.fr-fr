---
title: "Didacticiel : Intégration d’Azure Active Directory avec Pagerduty | Microsoft Docs"
description: "Apprenez à utiliser Pagerduty avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b473d7edb531f4fdab9c2ea8bde969e37558072


---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Didacticiel : Intégration d’Azure Active Directory à Pagerduty
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Pagerduty.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Pagerduty

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Pagerduty pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Pagerduty (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Pagerduty
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")

## <a name="enabling-the-application-integration-for-pagerduty"></a>Activation de l’intégration d’application pour Pagerduty
Cette section décrit l’activation de l’intégration d’application pour Pagerduty.

### <a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Pagerduty, procédez comme suit :
1. Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Pagerduty**.
   
   ![Galerie d’applications](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application gallery")
7. Dans le volet des résultats, sélectionnez **Pagerduty**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Pagerduty avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Pagerduty**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Pagerduty**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Pagerduty**, tapez votre URL d’après le modèle suivant « *https://\<nom_locataire\>.Pagerduty.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configure app url")
4. Dans la page **Configurer l’authentification unique sur Pagerduty**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Pagerduty en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Account Settings**.
   
   ![Account Settings](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")
7. Cliquez sur **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")
8. Dans la page Enable Single Sign-on (SSO), procédez comme suit :
   
   ![Activer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Enable single sign-on")
   
   1. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   2. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate** .
   3. Dans la boîte de dialogue **Configurer l’authentification unique sur Pagerduty** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Login URL**.
   4. Dans la boîte de dialogue **Configurer l’authentification unique sur Pagerduty** du portail Azure Classic, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Logout URL**.
   5. Sélectionnez **Turn on Single Sign-on**.
   6. Cliquez sur **Enregistrer les modifications**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Pagerduty, vous devez les approvisionner dans Pagerduty.  
Dans le cas de Pagerduty, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **Pagerduty** .
2. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
3. Cliquez sur **Add Users**.
   
   ![Ajouter des utilisateurs](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Add Users")
4. Dans la boîte de dialogue **Invite your team**, entrez le prénom, le nom et l’adresse e-mail de l’utilisateur Azure AD que vous voulez approvisionner, en l’occurrence dans les zones **First and Last Name** et **Email**, cliquez sur **Add**, puis sur **Send Invites**.
   
   ![Inviter dans votre équipe](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invite your team")
   
   > [!NOTE]
   > Tous les utilisateurs recevront une invitation pour créer un compte PagerDuty.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Pagerduty, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Pour affecter des utilisateurs à Pagerduty, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Pagerduty**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


