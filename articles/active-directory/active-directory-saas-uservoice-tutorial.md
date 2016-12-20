---
title: "Didacticiel : Intégration d’Azure Active Directory à UserVoice | Microsoft Docs"
description: "Découvrez comment utiliser UserVoice avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0bb23a4c2be40c6304d7a09a6ff978db0666ea18


---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Didacticiel : Intégration d’Azure Active Directory à UserVoice
L’objectif de ce didacticiel est de montrer comment intégrer Azure et UserVoice.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire UserVoice

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à UserVoice pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise UserVoice (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour UserVoice
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

## <a name="enabling-the-application-integration-for-uservoice"></a>Activation de l’intégration d’applications pour UserVoice
Cette section décrit l’activation de l’intégration d’applications pour UserVoice.

### <a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour UserVoice, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **UserVoice**.
   
   ![Galerie d’applications](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Application gallery")
7. Dans le volet des résultats, sélectionnez **UserVoice**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur UserVoice avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour UserVoice oblige à récupérer une valeur d’empreinte numérique dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **UserVoice** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à UserVoice**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")
3. Dans la zone de texte **URL de connexion de UserVoice** de la page **Configurer l’URL de l’application**, tapez votre URL au format « *https://\<nom_locataire\>.UserVoice.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur UserVoice**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **c:\\UserVoice.cer**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")
5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise UserVoice en tant qu’administrateur.
6. Dans la barre d’outils en haut, cliquez sur Settings et sélectionnez Web Portal dans le menu.
   
   ![Paramètres](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")
7. Dans la section **User authentication** de l’onglet **Web Portal**, cliquez sur **Edit** pour ouvrir la page de la boîte de dialogue **Edit User Authentication**.
   
   ![Web portal](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")
8. Dans la page **Edit User Authentication** , procédez comme suit :
   
   ![Edit User Authentication](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")
   
   1. Cliquez sur **Single Sign-On (SSO)**.
   2. Dans la page de la boîte de dialogue **Configurer l’authentification unique sur UserVoice** du portail Azure Classic, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Connexion distante SSO**.
   3. Dans la page de la boîte de dialogue **Configurer l’authentification unique sur UserVoice** du portail Azure Classic, copiez la valeur de **URL de déconnexion distante** et collez-la dans la zone de texte **Déconnexion distante SSO**.
   4. Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Current certificate SHA1 fingerprint**.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
      > 
      > 
   5. Cliquez sur **Save authentication settings**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour permettre aux utilisateurs Azure AD de se connecter à UserVoice, ils doivent être approvisionnés dans UserVoice.  
Dans le cas d’UserVoice, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **UserVoice** .
2. Accédez à **Settings**.
   
   ![Settings](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")
3. Cliquez sur **General**.
4. Cliquez sur **Agents and permissions**.
   
   ![Agents and permissions](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")
5. Cliquez sur **Add admins**.
   
   ![Add admins](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")
6. Dans la boîte de dialogue **Invite admins** , procédez comme suit :
   
   ![Invite admins](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")
   
   1. Dans la zone de texte Emails, entrez l’adresse de messagerie du compte que vous souhaitez approvisionner, puis cliquez sur **Add**.
   2. Cliquez sur **Inviter**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par UserVoice, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Pour affecter des utilisateurs à UserVoice, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **UserVoice**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


