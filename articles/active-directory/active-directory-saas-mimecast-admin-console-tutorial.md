---
title: "Didacticiel : Intégration d’Azure Active Directory avec Mimecast Admin Console | Microsoft Docs"
description: "Apprenez à utiliser Mimecast Admin Console avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7271ace1c6bd1d56e20106e09ba125ac59145d1d


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Didacticiel : Intégration d’Azure Active Directory avec Mimecast Admin Console
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Mimecast Admin Console.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Mimecast Admin Console pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Mimecast Admin Console pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Mimecast Admin Console (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Mimecast Admin Console
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Activation de l’intégration d’application pour Mimecast Admin Console
Cette section décrit l’activation de l’intégration d’application pour Mimecast Admin Console.

### <a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Mimecast Admin Console, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Mimecast Admin Console**.
   
   ![Galerie d’applications](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Mimecast Admin Console**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Mimecast Admin Console avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Mimecast Admin Console**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Mimecast Admin Console**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Mimecast Admin Console**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Mimecast Admin Console (par exemple : « https://webmail-fr.mimecast.com » ou « https://webmail-be.mimecast.com »), puis cliquez sur **Suivant**.
   
   > [!NOTE]
   > L’URL d’ouverture de session est spécifique à la région.
   > 
   > 
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Mimecast Admin Console**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Admin Console en tant qu’administrateur.
6. Accédez à **Services \> Application**.
   
   ![Services](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")
7. Cliquez sur **Authentication Profiles**.
   
   ![Authentication Profiles](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Authentication Profiles")
8. Cliquez sur **New Authentication Profile**.
   
   ![Nouveaux profils d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "New Authentication Profiles")
9. Dans la section **Authentication Profile** , procédez comme suit :
   
   ![Authentication Profile](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Authentication Profile")
   
   1. Dans la zone de texte **Description** , indiquez un nom pour votre configuration.
   2. Sélectionnez **Enforce SAML Authentication for Mimecast Admin Console**.
   3. Pour **Provider**, sélectionnez **Azure Active Directory**.
   4. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Mimecast Admin Console** de la boîte de dialogue, copiez la valeur de **URL de l’émetteur** et collez-la dans la zone de texte **Issuer URL**.
   5. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Mimecast Admin Console**, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Login URL**.
   6. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Mimecast Admin Console**, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Logout URL**.  
      
      > [!NOTE]
      > Les valeurs d’URL de connexion et de déconnexion sont identiques pour Mimecast Admin Console.
      > 
      > 
   7. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).
      > 
      > 
   8. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, supprimez la première (« *--* ») et la dernière ligne (« *--* »), copiez le contenu restant dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)**.
   9. Sélectionnez **Allow Single Sign On**.
   10. Cliquez sur **Save**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Admin Console, vous devez les approvisionner dans Mimecast Admin Console.  
Dans le cas de Mimecast Admin Console, l’approvisionnement est une tâche manuelle.

Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à **Mimecast Admin Console** en tant qu’administrateur.
2. Accédez à **Directories \> Internal**.
   
   ![Répertoires](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directories")
3. Cliquez sur **Register New Domain**.
   
   ![Register New Domain](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Register New Domain")
4. Après avoir créé votre domaine, cliquez sur **New Address**.
   
   ![New Address](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "New Address")
5. Dans la boîte de dialogue New Address, procédez comme suit :
   
   ![Enregistrer](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Save")
   
   1. Tapez l’adresse électronique, le nom global, le mot de passe et sa confirmation pour un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address**, **Global Name**, **Password** et **Confirm Password**.
   2. Cliquez sur **Save**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur, fourni par Mimecast Admin Console, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Pour affecter des utilisateurs à Mimecast Admin Console, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Mimecast Admin Console**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


