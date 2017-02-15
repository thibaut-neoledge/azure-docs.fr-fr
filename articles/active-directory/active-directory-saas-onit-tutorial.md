---
title: "Didacticiel : Intégration d’Azure Active Directory à Onit | Microsoft Docs"
description: "Apprenez à utiliser Onit avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80c727ba687e11f7407339b57bc1f6b0c091dd0f


---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Didacticiel : Intégration d’Azure Active Directory avec Onit
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Onit.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Onit pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Onit pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Onit (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Onit
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-onit-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-onit"></a>Activation de l’intégration d’application pour Onit
Cette section décrit l’activation de l’intégration d’application pour Onit.

### <a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Onit, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-onit-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-onit-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Onit**.
   
   ![Galerie d’applications](./media/active-directory-saas-onit-tutorial/IC791167.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Onit**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Onit avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Onit oblige à récupérer une valeur d’empreinte dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

Votre application Onit s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML** .  
La capture d’écran suivante montre un exemple :

![Authentification unique](./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Onit**, dans le menu en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.
   
   ![Attributs](./media/active-directory-saas-onit-tutorial/IC791169.png "Attributes")
2. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    |Nom de l'attribut|Valeur de l’attribut|
    |---|---|
    |name|User.userprincipalname|
    |email|User.mail|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **ajouter un attribut utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut pour cette ligne.
    3.  Dans la liste **Valeur de l’attribut** , sélectionnez la valeur d’attribut pour cette ligne.
    4.  Cliquez sur **Terminé**.

1. Cliquez sur **Appliquer les modifications**.
2. Dans votre navigateur, cliquez sur **Précédent** pour ouvrir la boîte de dialogue **Démarrage rapide**.
3. Cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/IC791170.png "Configure Single Sign-On")
4. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Onit ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/IC791171.png "Configure Single Sign-On")
5. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Onit**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Onit (par exemple, « *https://ms-sso-test.onit.com* »), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-onit-tutorial/IC791172.png "Configure App URL")
6. Dans la page **Configurer l’authentification unique sur Onit**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/IC791173.png "Configure Single Sign-On")
7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Onit en tant qu’administrateur.
8. Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")
9. Cliquez sur **Edit Corporation**.
   
   ![Edit Corporation](./media/active-directory-saas-onit-tutorial/IC791175.png "Edit Corporation")
10. Cliquez sur l’onglet **Security** .
    
    ![Modifier les informations de l’entreprise](./media/active-directory-saas-onit-tutorial/IC791176.png "Edit Company Information")
11. Sous l’onglet **Security** , procédez comme suit :
    
    ![Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")
    
    1. Comme **Authentication Strategy**, sélectionnez **Single Sign On and Password**.
    2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Onit** de la boîte de dialogue, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Idp Target URL**.
    3. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Onit**, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Idp logout URL**.
    4. Copiez la valeur **d’Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Idp Cert Fingerprint (SHA1)**.  
       
       > [!TIP]
       > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
       > 
       > 
    5. Pour **SSO Type**, sélectionnez **SAML**.
    6. Dans la zone de texte **SSO login button text** , tapez le texte souhaité pour le bouton.
    7. Sélectionnez **Login with SSO: Required for the following domains/users**, entrez l’adresse de messagerie d’un utilisateur de test dans la zone de texte correspondante, puis cliquez sur **Update**.
       ![Edit Corporation](./media/active-directory-saas-onit-tutorial/IC791178.png "Edit Corporation")
12. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-onit-tutorial/IC791179.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Onit, les utilisateurs d’Azure AD doivent être approvisionnés dans Onit.  
Dans le cas d’Onit, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous au site d’entreprise **Onit** en tant qu’administrateur.
2. Cliquez sur **Add User**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")
3. Dans la boîte de dialogue **Add user** , procédez comme suit :
   
   ![Ajouter un utilisateur](./media/active-directory-saas-onit-tutorial/IC791181.png "Add User")
   
   1. Tapez le nom et l’adresse électronique d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir **Name** et **Email Address**.
   2. Cliquez sur **Create**.  
      
      > [!NOTE]
      > Le titulaire du compte recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
      > 
      > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Onit pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-onit-perform-the-following-steps"></a>Pour affecter des utilisateurs à Onit, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Onit**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-onit-tutorial/IC791182.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-onit-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


