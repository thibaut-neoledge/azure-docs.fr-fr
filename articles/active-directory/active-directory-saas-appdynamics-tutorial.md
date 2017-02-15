---
title: "Didacticiel : Intégration d’Azure Active Directory à AppDynamics | Microsoft Docs"
description: "Apprenez à utiliser AppDynamics avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13f4e780afc26dc0f1f59408a77f68a7e46308c7


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Didacticiel : Intégration d’Azure Active Directory à AppDynamics
L’objectif de ce didacticiel est de montrer comment intégrer Azure et AppDynamics. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement AppDynamics pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à AppDynamics pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise AppDynamics (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour AppDynamics
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>Activation de l’intégration d’applications pour AppDynamics
Cette section décrit l’activation de l’intégration d’applications pour AppDynamics.

### <a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour AppDynamics, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **AppDynamics**.
   
   ![Galerie d’applications](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **AppDynamics**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur AppDynamics avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis, dans la page d’intégration d’application **AppDynamics**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à AppDynamics ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à AppDynamics ("*https://nomentreprise.saas.appdynamics.com*"), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. Dans la page **Configurer l'authentification unique sur AppDynamics** pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AppDynamics en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Paramètres**, puis sur **Administration**.
   
   ![Administration](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. Cliquez sur l’onglet **Authentication Provider** .
   
   ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. Dans la section **Authentication Provider** , procédez comme suit :
   
   ![Configuration SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")
   
   1. Dans **Fournisseur d’authentification**, sélectionnez **SAML**.
   2. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur AppDynamics**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Login URL**.
   3. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur AppDynamics**, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout URL**.
   4. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificate** .
   6. Cliquez sur **Save**.
      ![Save](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à AppDynamics, vous devez les approvisionner dans AppDynamics.  
En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise AppDynamics en tant qu’administrateur.
2. Accédez à **Users**, puis cliquez sur **+** pour ouvrir la boîte de dialogue **Create User**.
   
   ![Utilisateurs](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. Dans la section **Create User** , procédez comme suit :
   
   ![Create User](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. Tapez le **nom d’utilisateur**, le **nom**, **l’adresse e-mail**, le **nouveau mot de passe** et la **confirmation du nouveau mot de passe** d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
   2. Cliquez sur **Save**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par AppDynamics pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Pour affecter des utilisateurs à AppDynamics, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration de l’application **AppDynamics**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


