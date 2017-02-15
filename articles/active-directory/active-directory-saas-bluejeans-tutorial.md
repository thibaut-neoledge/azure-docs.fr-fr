---
title: "Didacticiel : Intégration d’Azure Active Directory à BlueJeans | Microsoft Docs"
description: "Apprenez à utiliser BlueJeans avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 00f00d91e54d35fb9009a3e927dfc8833cd7571e


---
# <a name="tutorial-azure-ad-integration-with-bluejeans"></a>Didacticiel : Intégration d’Azure Active Directory à BlueJeans
L’objectif de ce didacticiel est de montrer comment intégrer Azure et BlueJeans.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement BlueJeans pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à BlueJeans pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise BlueJeans (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour BlueJeans
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")

## <a name="enabling-the-application-integration-for-bluejeans"></a>Activation de l’intégration d’applications pour BlueJeans
Cette section décrit l’activation de l’intégration d’applications pour BlueJeans.

### <a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour BlueJeans, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **BlueJeans**.
   
   ![Galerie d’applications](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **BlueJeans**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur BlueJeans avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’applications **BlueJeans**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à BlueJeans ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à BlueJeans**, tapez votre URL au format suivant « *https://company.BlueJeans.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur BlueJeans**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.
6. Accédez à **ADMIN \> Group Settings \> Security**.
   
   ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")
7. Dans la section **Security** , procédez comme suit :
   
   ![SAML Single Sign On](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")
   
   1. Sélectionnez **SAML Single Sign On**.
   2. Sélectionnez **Enable automatic provisioning**.
8. Poursuivez en procédant comme suit :
   
   ![Chemin d’accès du certificat](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")
   
   1. Cliquez sur **Choose File**, puis chargez le certificat téléchargé.
   2. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur BlueJeans**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Login URL**.
   3. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur BlueJeans**, copiez la valeur **Modifier l’URL de mot de passe** et collez-la dans la zone de texte **Password Change URL**.
   4. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur BlueJeans**, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout URL**.
9. Poursuivez en procédant comme suit :
   
   ![Enregistrer les modifications](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")
   
   1. Dans la zone de texte **Identifiant utilisateur**, entrez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   2. Dans la zone de texte **E-mail**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   3. Cliquez sur **Enregistrer les modifications**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à BlueJeans, vous devez les approvisionner dans BlueJeans.  
En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.
2. Accédez à **ADMIN \> Manage Users \> Add User**.
   
   ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   > [!IMPORTANT]
   > L’onglet **Add User** est disponible uniquement si, sous l’onglet **Security**, l’option **Enable automatic provisioning** est décochée.
   > 
   > 
3. Dans la section **Add User** , procédez comme suit :
   
   ![Ajouter un utilisateur](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")
   
   1. Tapez un **nom d’utilisateur BlueJeans**, une **adresse de messagerie**, un **ID de réunion BlueJeans**, un **code secret de modérateur**, un **nom complet** et la **société** d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
   2. Cliquez sur **Add User**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par BlueJeans pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Pour affecter des utilisateurs à BlueJeans, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **BlueJeans**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


