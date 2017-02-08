---
title: "Didacticiel : intégration d’Azure Active Directory à Greenhouse | Microsoft Docs"
description: "Apprenez à utiliser Greenhouse avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 55a9a01414d930b40f60119318f5c023172141e1


---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Didacticiel : Intégration d’Azure Active Directory avec Greenhouse
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Greenhouse.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Greenhouse pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Greenhouse pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Greenhouse (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Greenhouse
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")

## <a name="enabling-the-application-integration-for-greenhouse"></a>Activation de l’intégration d’application pour Greenhouse
Cette section décrit l’activation de l’intégration d’application pour Greenhouse.

### <a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Pour activer l’application de l’intégration pour Greenhouse, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **greenhouse**.
   
   ![Galerie d’applications](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Greenhouse**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Greenhouse avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Greenhouse**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Greenhouse**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configure single sign-on")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle suivant *https://company.greenhouse.io*, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Greenhouse**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configure single sign-on")
5. Transférez ce fichier de métadonnées à l’équipe de support de Greenhouse
   
   > [!NOTE]
   > L’authentification unique doit être activée par l’équipe de support de Greenhouse.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Greenhouse, les utilisateurs d’Azure AD doivent être approvisionnés dans Greenhouse.  
Dans le cas de Greenhouse, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous au site d’entreprise **Greenhouse** en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur **Configure (Configurer)**, puis sur **Users (Utilisateurs)**.
   
   ![Utilisateurs](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Users")
3. Cliquez sur **New Users**.
   
   ![Nouvel utilisateur](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "New User")
4. Dans la section **Add New User** , procédez comme suit :
   
   ![Add New User](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Add New User")
   
   1. Dans la zone de texte **Enter user emails** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide à approvisionner.
   2. Cliquez sur **Enregistrer**.
      
      > [!NOTE]
      > Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
      > 
      > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Greenhouse pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Pour affecter des utilisateurs à Greenhouse, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Greenhouse**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


