---
title: "Didacticiel : Intégration d’Azure Active Directory avec Picturepark | Microsoft Docs"
description: "Apprenez à utiliser Picturepark avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 98f2e5596a0af2fc9e633e005642cc3cd21621ce


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Didacticiel : Intégration d’Azure Active Directory à Picturepark
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Picturepark.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Picturepark

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Picturepark pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Picturepark (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Picturepark
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")

## <a name="enabling-the-application-integration-for-picturepark"></a>Activation de l’intégration d’application pour Picturepark
Cette section décrit l’activation de l’intégration d’application pour Picturepark.

### <a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Picturepark, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Picturepark**.
   
   ![Galerie d’applications](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Picturepark**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Picturepark avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Picturepark vous oblige à récupérer une valeur d’empreinte dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Picturepark**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Picturepark**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Picturepark**, tapez votre URL selon le modèle suivant : « *http://company.picturepark.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Picturepark**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Picturepark en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Administrative tools**, puis sur **Management Console**.
   
   ![Management Console](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Management Console")
7. Cliquez sur **Authentication**, puis sur **Identity providers**.
   
   ![Authentification](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentication")
8. Dans la section **Identity provider configuration** , procédez comme suit :
   
   ![Identity provider configuration](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Identity provider configuration")
   
   1. Cliquez sur **Add**.
   2. Entrez un nom pour votre configuration.
   3. Sélectionnez **Set as default**.
   4. Dans la page **Configurer l’authentification unique sur Picturepark** du portail Azure Classic, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Issuer URI**.
   5. Copiez la valeur **Empreinte numérique** du certificat exporté, puis collez-la dans la zone de texte **Trusted Issuer Thumb Print**.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Cliquez sur **JoinDefaultUsersGroup**.
   7. Pour définir l’attribut **Emailaddress** dans la zone de texte **Revendication**, saisissez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
      ![Configuration](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
   8. Cliquez sur **Save**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour permettre aux utilisateurs Azure AD de se connecter à Picturepark, vous devez les approvisionner dans Picturepark.  
Dans le cas de Picturepark, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **Picturepark** .
2. Dans la barre d’outils située en haut, cliquez sur **Administrative tools**, puis sur **Users**.
   
   ![Utilisateurs](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Users")
3. Dans l’onglet **Users overview**, cliquez sur **New**.
   
   ![User Management](./media/active-directory-saas-picturepark-tutorial/IC795068.png "User management")
4. Dans la boîte de dialogue **Create User** , procédez comme suit :
   
   ![Create User](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Create User")
   
   1. Tapez l’adresse de messagerie, le mot de passe, la confirmation du mot de passe, le prénom, le nom, la société, le pays et le code postal d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les champs correspondants, à savoir, **Email Address**, **Password**, **Confirm Password**, **First Name**, **Last Name**, **Company**, **Country**, **ZIP** et **City**.
   2. Sélectionnez une langue dans **Language**.
   3. Cliquez sur **Create**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Picturepark, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Pour affecter des utilisateurs à Picturepark, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Picturepark**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


