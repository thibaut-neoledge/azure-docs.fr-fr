---
title: "Didacticiel : Intégration d’Azure Active Directory à BambooHR | Microsoft Docs"
description: "Apprenez à utiliser BambooHR avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e675b92ea53c31c014e20735c039db3dbad325cc


---
# <a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Didacticiel : Intégration d’Azure Active Directory à BambooHR
L’objectif de ce didacticiel est de montrer comment intégrer Azure et BambooHR.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement BambooHR pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à BambooHR pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise BambooHR (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour BambooHR
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scénario")

## <a name="enabling-the-application-integration-for-bamboohr"></a>Activation de l’intégration d’applications pour BambooHR
Cette section décrit l’activation de l’intégration d’applications pour BambooHR.

### <a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour BambooHR, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **BambooHR**.
   
   ![Galerie d’applications](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **BambooHR**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur BambooHR avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **BambooHR**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Scénario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scénario")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à BambooHR ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application BambooHR (par exemple, https://company.bamboohr.com) puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur BambooHR**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise BambooHR en tant qu’administrateur.
6. Dans la page d’accueil, procédez comme suit :
   
   ![Authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Authentification unique")
   
   1. Cliquez sur **Applications**.
   2. Dans le menu des applications sur la gauche, cliquez sur **Single Sign-On**.
   3. Cliquez sur **SAML Single Sign-On**.
7. Dans la section **SAML Single Sign-On** , procédez comme suit :
   
   ![Authentification unique SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Authentification unique SAML")
   
   1. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur BambooHR**, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **SSO Login URL**.
   2. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   3. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .
   4. Cliquez sur **Save**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurer l’authentification unique")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à BambooHR, vous devez les approvisionner dans BambooHR.  
En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site **BambooHR** en tant qu’administrateur.
2. Dans la barre d’outils située en haut, cliquez sur **Settings**.
   
   ![Paramètre](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Paramètre")
3. Cliquez sur **Overview**.
4. Dans le volet de navigation de gauche, accédez à **Security \> Users**.
5. Tapez le nom d’utilisateur, le mot de passe et l’adresse de messagerie d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
6. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par BambooHR pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Pour affecter des utilisateurs à BambooHR, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **BambooHR**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


