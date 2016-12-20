---
title: "Didacticiel : intégration d’Azure Active Directory à Thoughtworks Mingle | Microsoft Docs"
description: "Découvrez comment utiliser Thoughtworks Mingle avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6318f2fad781118b0f2c364d1cbc2b686f46a95e


---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Didacticiel : Intégration d’Azure AD à Thoughtworks Mingle
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Thoughtworks Mingle.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Thoughtworks Mingle

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Thoughtworks Mingle
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenario")

## <a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Activation de l’intégration d’applications pour Thoughtworks Mingle
Cette section décrit l’activation de l’intégration de l’application pour Thoughtworks Mingle.

### <a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Thoughtworks Mingle, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **thoughtworks mingle**.
   
   ![Galerie d’applications](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Thoughtworks Mingle**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Thoughtworks Mingle avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez charger un certificat dans Thoughtworks Mingle.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Thoughtworks Mingle** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Thoughtworks Mingle**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configure single sign-on")
3. Dans la zone de texte **URL du locataire Thoughtworks Mingle** de la page **Configurer l’URL de l’application**, tapez votre URL au format *http://company.mingle.thoughtworks.com*, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Thoughtworks Mingle** , cliquez sur Télécharger les métadonnées, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configure single sign-on")
5. Connectez-vous à votre site d’entreprise **Thoughtworks Mingle** en tant qu’administrateur.
6. Cliquez sur l’onglet **Admin**, puis sur **SSO Config (Configuration SSO)**.
   
   ![SSO Config](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")
7. Dans la section **SSO Config** , procédez comme suit :
   
   ![SSO Config](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")
   
   1. Pour charger le fichier de métadonnées, cliquez sur **Choose file**.
   2. Cliquez sur **Enregistrer les modifications**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Thoughtworks Mingle à l’aide de leurs noms d’utilisateur Azure AD.  
Dans le cas de Thoughtworks Mingle, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise Thoughtworks Mingle en tant qu’administrateur.
2. Cliquez sur **Profile**.
   
   ![Votre premier projet](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Your First Project")
3. Cliquez sur l’onglet **Admin**, puis sur **Users (Utilisateurs)**.
   
   ![Utilisateurs](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Users")
4. Cliquez sur **New User**.
   
   ![New User](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "New User")
5. Dans la boîte de dialogue **New User** , procédez comme suit :
   
   ![Nouvel utilisateur](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "New User")
   
   1. Renseignez les zones de texte **Sign-in name (Nom de connexion)**, **Display name (Nom d’affichage)**, **Choose password (Choisir le mot de passe)** et **Confirm password (Confirmer le mot de passe)** du compte AAD valide que vous souhaitez approvisionner.
   2. Dans **User type (Type d’utilisateur)**, sélectionnez **Full user (Utilisateur complet)**.
   3. Cliquez sur **Create This Profile**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par Thoughtworks Mingle, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Pour affecter des utilisateurs à Thoughtworks Mingle, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Thoughtworks Mingle**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


