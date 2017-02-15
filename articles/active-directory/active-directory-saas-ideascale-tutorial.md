---
title: "Didacticiel : Intégration d’Azure Active Directory avec IdeaScale | Microsoft Docs"
description: "Apprenez à utiliser IdeaScale avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f5e9f3ae668cc1f70bb6c0b1abc0e6898f4aaa48


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Didacticiel : Intégration d’Azure Active Directory à IdeaScale
L’objectif de ce didacticiel est de montrer comment intégrer Azure et IdeaScale.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement IdeaScale pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à IdeaScale pourront s’authentifier de manière unique dans l’application à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour IdeaScale
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")

## <a name="enabling-the-application-integration-for-ideascale"></a>Activation de l’intégration d’application pour IdeaScale
Cette section décrit l’activation de l’intégration d’application pour IdeaScale.

### <a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Pour activer l’intégration d’application pour IdeaScale, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **IdeaScale**.
   
   ![Galerie d’applications](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **IdeaScale**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur IdeaScale avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour IdeaScale vous oblige à récupérer une valeur d’empreinte dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **IdeaScale**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à IdeaScale**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configure Single Sign-On")
3. Dans la zone de texte **URL de connexion de IdeaScale** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application IdeaScale (par exemple : « *https://company.IdeaScale.com* »), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur IdeaScale**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise IdeaScale en tant qu’administrateur.
6. Accédez à **Community Settings**.
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
7. Accédez à **Security \> Single Signon Settings**.
   
   ![Paramètres de l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")
8. Pour **Single-Signon Type**, sélectionnez **SAML 2.0**.
   
   ![Type d’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. Dans la page de boîte de dialogue **Paramètres de l’authentification unique** , procédez comme suit :
   
   ![Paramètres de l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")
   
   1. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur IdeaScale**, copiez la valeur **ID d’identité**, puis collez-la dans la zone de texte **SAML IdP Entity ID**.
   2. Copiez le contenu du fichier de métadonnées téléchargé et collez-le dans la zone de texte **SAML ldP Metadata** .
   3. Dans la page **Configurer l’authentification unique sur IdeaScale** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout Success URL**.
   4. Cliquez sur **Enregistrer les modifications**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à IdeaScale, vous devez les approvisionner dans IdeaScale.  
Dans le cas d’IdeaScale, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **IdeaScale** en tant qu’administrateur.
2. Accédez à **Community Settings**.
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
3. Accédez à **Basic Settings \> Member Management**.
4. Cliquez sur **Add Member**.
   
   ![Gestion des membres](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. Dans la section Add New Member, procédez comme suit :
   
   ![Ajouter un nouvel membre](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. Indiquez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans la zone de texte **Email Addresses** .
   2. Cliquez sur **Save Changes**.
   
   > [!NOTE]
   > Le titulaire du compte Azure Active Directory reçoit un message électronique avec un lien pour confirmer le compte avant qu’il ne soit activé.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par IdeaScale pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Pour affecter des utilisateurs à IdeaScale, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **IdeaScale**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


