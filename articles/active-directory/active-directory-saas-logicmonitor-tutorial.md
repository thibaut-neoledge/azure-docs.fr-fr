---
title: "Didacticiel : Intégration d’Azure Active Directory avec LogicMonitor | Microsoft Docs"
description: "Apprenez à utiliser LogicMonitor avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6dbb19605f92ff0c0d58478a56e564bab71123e4


---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Didacticiel : Intégration d’Azure Active Directory à LogicMonitor
L’objectif de ce didacticiel est de montrer comment intégrer Azure et LogicMonitor.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire LogicMonitor

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour LogicMonitor
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")

## <a name="enabling-the-application-integration-for-logicmonitor"></a>Activation de l’intégration d’application pour LogicMonitor
Cette section décrit l’activation de l’intégration d’application pour LogicMonitor.

### <a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Pour activer l’intégration d’application pour LogicMonitor, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **LogicMonitor**.
   
   ![Galerie d’applications](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **LogicMonitor**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur LogicMonitor avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **LogicMonitor**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à LogicMonitor**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à LogicMonitor \(par ex. *http://company.logicmonitor.com*\), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur LogicMonitor**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")
5. Connectez-vous au site d’entreprise **LogicMonitor** en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Settings**.
   
   ![Settings](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")
7. Dans la barre de navigation située à gauche, cliquez sur **Single Sign On**
   
   ![Authentification unique](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")
8. Dans la section **Single Sign-On SSO settings** , procédez comme suit :
   
   ![Paramètres d’authentification unique](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")
   
   1. Sélectionnez **Enable Single Sign-On**.
   2. Pour **Default Role Assignment**, sélectionnez **readonly**.
   3. Ouvrez le fichier de métadonnées téléchargé dans le Bloc-notes, puis collez le contenu dans la zone de texte **Identity Provider Metadata** .
   4. Cliquez sur **Enregistrer les modifications**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour AAD les utilisateurs puissent se connecter, ils doivent être approvisionnés dans l’application LogicMonitorà l’aide de leurs noms d’utilisateur Azure Active Directory.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous au site d’entreprise LogicMonitor en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur **Settings**, puis sur **Roles and Users**.
   
   ![Roles and Users](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")
3. Cliquez sur **Ajouter**.
4. Dans la section **Add an account** , procédez comme suit :
   
   ![Ajouter un compte](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")
   
   1. Tapez le nom d’utilisateur, l’adresse de messagerie, le mot de passe et sa confirmation pour l’utilisateur Azure Active Directory que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Username**, **Email**, **Password** et **Retype Password**.
   2. Sélectionnez **Roles**, **View Permissions** et **Status**.
   3. Cliquez sur **Envoyer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par LogicMonitor, pour approvisionner des comptes utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Pour affecter des utilisateurs à LogicMonitor, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **LogicMonitor**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


