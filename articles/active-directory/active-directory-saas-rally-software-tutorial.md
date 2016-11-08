---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Rally Software | Microsoft Docs'
description: Apprenez à utiliser Rally Software avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-rally-software"></a>Didacticiel : Intégration d’Azure Active Directory avec Rally Software
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Rally Software.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Rally Software

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Rally Software
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")

## <a name="enabling-the-application-integration-for-rally-software"></a>Activation de l’intégration d’application pour Rally Software
Cette section décrit l’activation de l’intégration d’application pour Rally Software.

### <a name="to-enable-the-application-integration-for-rally-software,-perform-the-following-steps:"></a>Pour activer l’intégration d’application pour Rally Software, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **rally**.
   
   ![Galerie d’applications](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Application gallery")
7. Dans le volet des résultats, sélectionnez **Rally Software**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Rally Software avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez charger un certificat vers Rally Software.

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Rally Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Rally**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Authentification unique Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Rally Software**, tapez votre URL selon le modèle suivant « *https://\<nom-locataire\>.rally.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Rally** , cliquez sur Télécharger les métadonnées, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")
5. Connectez-vous à votre locataire **Rally Software** .
6. Dans la barre d’outils située en haut, cliquez sur **Setup**, puis sélectionnez **Subscription**.
   
   ![abonnement](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")
7. Cliquez sur le bouton **Action** dans la barre d’outils située sur la droite, puis sélectionnez **Edit Subscription**.
8. Dans la page **Subscription**, procédez comme suit, puis cliquez sur **Save & Close** :
   
   ![Authentification](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentication")
   
   1. Sélectionnez **Rally or SSO authentication** dans la liste déroulante Authentication.
   2. Dans la page **Configurer l’authentification unique sur Rally Software** du portail Azure Classic, copiez la valeur de **ID de fournisseur d’identité** et collez-la dans la zone de texte **Identity Provider URL**
   3. Dans la page **Configurer l’authentification unique sur Rally Software** du portail Azure Classic, copiez la valeur **URL de déconnexion distante**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour AAD les utilisateurs puissent se connecter, ils doivent être approvisionnés dans l’application Rally Software à l’aide de leurs noms d’utilisateur Azure Active Directory.

### <a name="to-configure-user-provisioning,-perform-the-following-steps:"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre locataire Rally Software.
2. Accédez à **Setup \> USERS**, puis cliquez sur **+ Add New**.
   
   ![Utilisateurs](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")
3. Tapez le nom dans la zone de texte New User, puis cliquez sur **Add with Details**.
4. Dans la section **Create User** , procédez comme suit :
   
   ![Create User](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Create User")
   
   1. Dans la zone de texte **User Name** , tapez le nom de l’utilisateur Azure AD que vous souhaitez approvisionner.
   2. Dans la zone de texte **Email Address** , tapez l’adresse de messagerie de l’utilisateur Azure AD que vous souhaitez approvisionner.
   3. Cliquez sur **Enregistrer et fermer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Rally Software pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-rally-software,-perform-the-following-steps:"></a>Pour affecter des utilisateurs à Rally Software, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Rally Software**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


