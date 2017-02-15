---
title: "Didacticiel : Intégration d’Azure Active Directory avec Mozy Enterprise | Microsoft Docs"
description: "Apprenez à utiliser Mozy Enterprise avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6034434c289936089afbb0fa5bd803363cde882


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory avec Mozy Enterprise
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Mozy Enterprise.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Mozy Enterprise

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Mozy Enterprise pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Mozy Enterprise (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Mozy Enterprise
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>Activation de l’intégration d’application pour Mozy Enterprise
Cette section décrit l’activation de l’intégration d’application pour Mozy Enterprise.

### <a name="to-enable-the-application-integration-for-mozy-enterprise-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Mozy Enterprise, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Mozy Enterprise**.
   
   ![Galerie d’applications](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Mozy Enterprise**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Mozy Enterprise avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez charger un certificat codé en base 64 vers votre locataire Mozy Enterprise.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Mozy Enterprise**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Mozy Enterprise**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configure single sign-on")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Mozy Enterprise**, tapez votre URL d’après le modèle suivant « *https://\<nom_locataire\>.Mozyenterprise.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configure app URL")
4. Dans la page **Configurer l’authentification unique sur Mozy Enterprise**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configure single sign-on")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mozy Enterprise en tant qu’administrateur.
6. Dans la section **Configuration**, cliquez sur **Authentication Policy**.
   
   ![Authentication Policy](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentication policy")
7. Dans la section **Authentication Policy** , procédez comme suit :
   
   ![Authentication Policy](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentication policy")
   
   1. Sélectionnez **Directory Service** comme **Provider**.
   2. Sélectionnez **Use LDAP Push**.
   3. Cliquez sur l’onglet **SAML Authentication** .
   4. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Mozy Enterprise** de la boîte de dialogue, copiez la valeur **URL de la requête d’authentification** et collez-la dans la zone de texte **Authentication URL**.
   5. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Mozy Enterprise**, copiez la valeur **ID de fournisseur d’identité** et collez-la dans la zone de texte **SAML Endpoint**.
   6. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **SAML Certificate** .
   8. Sélectionnez **Enable SSO for Admins to log in with their network credentials**.
   9. Cliquez sur **Enregistrer les modifications**.
8. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Mozy Enterprise**, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Mozy Enterprise, vous devez les approvisionner dans Mozy Enterprise.  
Dans le cas de Mozy Enterprise, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **Mozy Enterprise** .
2. Cliquez sur **Users**, puis sur **Add New User**.
   
   ![Users](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Users")
   
   > [!NOTE]
   > L’option **Add New User** ne s’affiche que si **Mozy** est sélectionné comme fournisseur sous **Authentication policy**. Si l’authentification SAML est configurée, les utilisateurs sont ajoutés automatiquement à leur première connexion à l’aide de l’authentification unique.
   > 
   > 
3. Dans la boîte de dialogue New User, procédez comme suit :
   
   ![Ajouter des utilisateurs](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Add Users")
   
   1. Dans la liste **Choose a Group** , sélectionnez un groupe.
   2. Dans la liste **What type of user** , sélectionnez un type.
   3. Dans la zone de texte **Username** , tapez le nom de l’utilisateur Azure AD.
   4. Dans la zone de texte **Email** , tapez l’adresse de messagerie de l’utilisateur Azure AD.
   5. Sélectionnez **Send user instruction email**.
   6. Cliquez sur **Add User(s)**.
   
   > [!NOTE]
   > Après la création de l’utilisateur, un message électronique sera envoyé à l’utilisateur Azure AD avec un lien pour confirmer le compte avant qu’il ne soit activé.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Mozy Enterprise pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-mozy-enterprise-perform-the-following-steps"></a>Pour affecter des utilisateurs à Mozy Enterprise, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Mozy Enterprise**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


