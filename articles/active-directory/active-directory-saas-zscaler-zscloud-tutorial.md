---
title: "Didacticiel : Intégration d’Azure Active Directory à Zscaler ZSCloud | Microsoft Docs"
description: "Découvrez comment utiliser Zscaler ZSCloud avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7d5438776882d8ce3d93718d35efe9fa2562478f
ms.openlocfilehash: 455a32848d4f49071bb4d81574c41f65349078e1
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Didacticiel : Intégration d’Azure Active Directory à Zscaler ZSCloud
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Zscaler ZSCloud.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Zscaler ZSCloud pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Zscaler ZSCloud pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Zscaler ZSCloud (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Zscaler ZSCloud
2. Configuration de l'authentification unique
3. Configuration des paramètres de proxy
4. Configuration de l'approvisionnement des utilisateurs
5. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800275.png "Scénario")

## <a name="enabling-the-application-integration-for-zscaler-zscloud"></a>Activation de l’intégration d’applications pour Zscaler ZSCloud
Cette section décrit l’activation de l’intégration d’applications pour Zscaler ZSCloud.

### <a name="to-enable-the-application-integration-for-zscaler-zscloud-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Zscaler ZSCloud, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Zscaler ZSCloud**.
   
   ![Galerie d’applications](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800276.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Zscaler ZSCloud**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![ZScaler ZSCloud](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800277.png "ZScaler ZSCloud")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Zscaler ZSCloud avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez charger un certificat codé en base 64 vers votre client Zscaler ZSCloud.  
Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Zscaler ZSCloud** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800278.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Zscaler ZSCloud**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800279.png "Configurer l’authentification unique")
3. Dans la zone de texte **URL de connexion à Zscaler ZSCloud** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Zscaler ZSCloud, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800280.png "Configurer l’URL de l’application")
   
   > [!NOTE]
   > Vous pouvez obtenir la valeur réelle de votre environnement auprès de l’équipe du support technique Zscaler ZSCloud.
   > 
   > 
4. Dans la page **Configurer l’authentification unique sur Zscaler ZSCloud**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800281.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler ZSCloud en tant qu’administrateur.
6. Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.
   
   ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800206.png "Administration")
7. Sous **Manage Administrators & Roles**, cliquez sur **Manage Users & Authentication**.
   
   ![Manage Users & Authentication (Gérer les utilisateurs et l’authentification)](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800207.png "Manage Users & Authentication (Gérer les utilisateurs et l’authentification)")
8. Dans la section **Choose Authentication Options for your Organization** , procédez comme suit :
   
   ![Authentication](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800208.png "Authentication")
   
   1. Sélectionnez **Authenticate using SAML Single Sign-On**.
   2. Cliquez sur **Configure SAML Single Sign-On Parameters**.
9. Dans la boîte de dialogue **Configurer les paramètres d’authentification unique SAML**, procédez comme suit, puis cliquez sur **OK** :
   
   ![Authentification unique](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800209.png "Authentification unique")
   
   1. Dans la page **Configurer l’authentification unique sur ZScaler ZSCloud** du portail Azure Classic, copiez la valeur **URL de demande d’authentification**, puis collez-la dans la zone de texte **URL of the SAML Portal to which users are sent for authentication** (URL du portail SAML vers lequel les utilisateurs sont renvoyés pour l’authentification).
   2. Dans la zone de texte **Attribute containing Login Name**, tapez **NameID**.
   3. Pour charger le certificat téléchargé, cliquez sur **Zscaler pem**.
   4. Sélectionnez **Enable SAML Auto-Provisioning**.
10. Dans la page **Configure User Authentication** , procédez comme suit :
    
    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800210.png "Administration")
    
    1. Cliquez sur **Save**.
    2. Cliquez sur **Activate Now**.
11. Dans la boîte de dialogue **Configurer l’authentification unique sur Zscaler ZSCloud** du portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800282.png "Configurer l’authentification unique")

## <a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer
1. Démarrez **Internet Explorer**.
2. Dans le menu **Outils**, sélectionnez **Options Internet** pour ouvrir la boîte de dialogue **Options Internet**.
   
   ![Options Internet](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769492.png "Options Internet")
3. Cliquez sur l’onglet **Connexions** .
   
   ![Connexions](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769493.png "Connexions")
4. Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.
5. Dans la section Serveur proxy, procédez comme suit :
   
   ![Serveur proxy](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769494.png "Serveur proxy")
   
   1. Sélectionnez Utiliser un serveur proxy pour votre réseau local.
   2. Dans la zone de texte Adresse, tapez **gateway.zscalerone.net**.
   3. Dans la zone de texte Port, tapez **80**.
   4. Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.
   5. Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.
6. Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour pouvoir se connecter à Zscaler ZSCloud, les utilisateurs d’Azure AD doivent être approvisionnés dans Zscaler ZSCloud.  
Dans le cas de Zscaler ZSCloud, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous au locataire **Zscaler** .
2. Cliquez sur **Administration**.
   
   ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781035.png "Administration")
3. Cliquez sur **User Management**.
   
   ![Add (Ajouter)](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Add (Ajouter)")
4. Sous l’onglet **Utilisateurs**, cliquez sur **Ajouter**.
   
   ![Add (Ajouter)](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Add (Ajouter)")
5. Dans la section Add User, procédez comme suit :
   
   ![Ajouter un utilisateur](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781038.png "Ajouter un utilisateur")
   
   1. Renseignez les zones de texte **UserID**, **Nom d’affichage de l’utilisateur**, **Mot de passe** et **Confirmer le mot de passe**, puis sélectionnez **Groupes** ainsi que l’attribut **Département** du compte AAD valide que vous souhaitez approvisionner.
   2. Cliquez sur **Save**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zscaler ZSCloud pour approvisionner des comptes d’utilisateurs Azure AD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-zscaler-zscloud-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zscaler ZSCloud, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Zscaler ZSCloud**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800283.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-zscaler-zscloud-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


