---
title: "Didacticiel : Intégration d’Azure Active Directory à Zoom | Microsoft Docs"
description: "Découvrez comment utiliser Zoom avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 80668119e273aaaa7d36d2c093fba344ea50e3d8


---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Didacticiel : Intégration d’Azure Active Directory à Zoom
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Zoom.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un client Zoom

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Zoom pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Zoom (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Zoom
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-zoom-tutorial/IC784693.png "Scenario")

## <a name="enabling-the-application-integration-for-zoom"></a>Activation de l’intégration d’applications pour Zoom
Cette section décrit l’activation de l’intégration d’applications pour Zoom.

### <a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Zoom, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-zoom-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-zoom-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-zoom-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Zoom**.
   
   ![Galerie d’applications](./media/active-directory-saas-zoom-tutorial/IC784694.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Zoom**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Zoom avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Zoom** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zoom-tutorial/IC784696.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Zoom**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zoom-tutorial/IC784697.png "Configure single sign-on")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Zoom**, tapez l’URL d’après le modèle « *http://company.zoom.us* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-zoom-tutorial/IC784698.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Zoom**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zoom-tutorial/IC784699.png "Configure single sign-on")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoom en tant qu’administrateur.
6. Cliquez sur l’onglet **Single Sign-On** .
   
   ![Authentification unique](./media/active-directory-saas-zoom-tutorial/IC784700.png "Single sign-on")
7. Cliquez sur l’onglet **Security Control**, puis accédez aux paramètres **Single Sign-On**.
8. Dans la section Single Sign-On, procédez comme suit :
   
   ![Authentification unique](./media/active-directory-saas-zoom-tutorial/IC784701.png "Single sign-on")
   
   1. Dans la page **Configurer l’authentification unique sur Zoom** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Sign-in page URL** (URL de la page de connexion).
   2. Dans la page **Configurer l’authentification unique sur Zoom** du portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Sign-out page URL** (URL de la page de connexion).
   3. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate** .
   5. Dans la page **Configurer l’authentification unique sur Zoom** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer** (Émetteur).
   6. Cliquez sur **Save**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-zoom-tutorial/IC784702.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour pouvoir se connecter à Zoom, les utilisateurs d’Azure AD doivent être approvisionnés dans Zoom.  
Dans le cas de Zoom, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **Zoom** en tant qu’administrateur.
2. Cliquez sur l’onglet **Account Management**, puis sur **User Management**.
3. Dans la section User Management, cliquez sur **Add users**.
   
   ![User Management](./media/active-directory-saas-zoom-tutorial/IC784703.png "User management")
4. Dans la page **Add users** , procédez comme suit :
   
   ![Ajouter des utilisateurs](./media/active-directory-saas-zoom-tutorial/IC784704.png "Add users")
   
   1. Comme **User Type**, sélectionnez **Basic**.
   2. Tapez l’adresse de messagerie du compte Azure AD valide que vous souhaitez approvisionner dans la zone de texte **Emails** .
   3. Cliquez sur **Ajouter**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zoom pour approvisionner des comptes d’utilisateurs Azure AD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zoom, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Zoom**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-zoom-tutorial/IC784705.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-zoom-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


