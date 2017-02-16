---
title: "Didacticiel : Intégration d’Azure Active Directory à Boomi | Microsoft Docs"
description: "Apprenez à utiliser Boomi avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a315916e-8bfd-4390-bad2-ec9029314ab6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51ea1ededc7b850e3dff24c9f735789d34391434


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Didacticiel : Intégration d’Azure Active Directory à Boomi
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Boomi.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Boomi pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Boomi pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Boomi (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Boomi
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")

## <a name="enabling-the-application-integration-for-boomi"></a>Activation de l’intégration d’applications pour Boomi
Cette section décrit l’activation de l’intégration d’applications pour Boomi.

### <a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Boomi, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Boomi**.
   
   ![Galerie d’applications](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Boomi**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Boomi avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Boomi** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Boomi**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de réponse Boomi**, tapez votre **URL de connexion Boomi AtomSphere** (par exemple, « *https://platform.boomi.com/sso/AccountName/saml* »), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Boomi**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur le nom de votre société, puis sur **Setup**.
   
   ![Setup](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")
7. Cliquez sur **Options d’authentification unique**.
   
   ![Options d’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")
8. Dans la section **Single Sign-On Options** , procédez comme suit :
   
   ![Single Sign-On Options](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")
   
   1. Sélectionnez **Enable SAML Single Sign-On**.
   2. Pour charger le certificat téléchargé, cliquez sur **Import**.
   3. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Boomi**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Identity Provider Login URL**.
   4. Dans **Federation Id Location**, sélectionnez **Federation Id is in NameID element of the Subject**.
   5. Cliquez sur **Enregistrer**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Boomi, vous devez les approvisionner dans Boomi.  
En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **Boomi** en tant qu’administrateur.
2. Accédez à **User Management \> Users**.
   
   ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")
3. Cliquez sur **Add User**.
   
   ![Add User](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")
4. Dans la page de boîte de dialogue **Add User Roles** , procédez comme suit :
   
   ![Ajouter un utilisateur](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")
   
   1. Indiquez le prénom, le nom et l’adresse de messagerie du compte AAD valide que vous souhaitez approvisionner dans les zones de texte **First Name**, **Last Name** et **Email**.
   2. Cliquez sur OK.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Boomi pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Pour affecter des utilisateurs à Boomi, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Boomi**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


