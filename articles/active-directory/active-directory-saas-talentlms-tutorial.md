---
title: "Didacticiel : intégration d’Azure Active Directory à TalentLMS | Microsoft Docs"
description: "Découvrez comment utiliser TalentLMS avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 7e1f1ccd109a2777f6d5df96b5f5d02eefc4b841
ms.lasthandoff: 12/08/2016


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Didacticiel : Intégration d’Azure AD à TalentLMS
L’objectif de ce didacticiel est de montrer comment intégrer Azure et TalentLMS.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire TalentLMS

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à TalentLMS pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise TalentLMS (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour TalentLMS
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scénario")

## <a name="enabling-the-application-integration-for-talentlms"></a>Activation de l’intégration d’applications pour TalentLMS
Cette section décrit l’activation de l’intégration d’applications pour TalentLMS.

### <a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour TalentLMS, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **TalentLMS**.
   
    ![Galerie d’applications](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galerie d’applications")

7. Dans le volet de résultats, sélectionnez **TalentLMS**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur TalentLMS avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. .  
La configuration de l’authentification unique pour TalentLMS oblige à récupérer une valeur d’empreinte numérique dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **TalentLMS** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à TalentLMS**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurer l’authentification unique")
3. Dans la zone de texte **URL de connexion à TalentLMS** de la page **Configurer l’URL de l’application**, tapez votre URL au format *https://\<nom-client\>.TalentLMSapp.com*, puis cliquez sur **Suivant**.
   
    ![URL de connexion](./media/active-directory-saas-talentlms-tutorial/IC777294.png "URL de connexion")

4. Dans la page **Configurer l’authentification unique sur TalentLMS**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **c:\\TalentLMS.cer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TalentLMS en tant qu’administrateur.

6. Dans la section **Account & Settings (Compte et paramètres)**, cliquez sur l’onglet **Users (Utilisateurs)**.
   
    ![Compte et paramètres](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Compte et paramètres")

7. Cliquez sur **Single Sign-On (SSO)**.

8. Dans la section Single Sign-On, procédez comme suit :
   
    ![Authentification unique](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Authentification unique")
   
    a. Dans la liste **SSO integration type (Type d’intégration SSO)**, sélectionnez **SAML 2.0**.
   
    b. Dans la page de boîte de dialogue **Configurer l’authentification unique sur TalentLMS** du portail Azure Classic, copiez la valeur **ID de fournisseur d’identité** et collez-la dans la zone de texte **Identity provider (IdP) (Fournisseur d’identité) (Idp)**.
   
    c. Copiez la valeur **Empreinte** du certificat exporté, puis collez-la dans la zone de texte **Certificate Fingerprint**.
      
    > [!TIP]
    > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    d. Dans la page de boîte de dialogue **Configurer l’authentification unique sur TalentLMS** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Remote sign-in URL (URL de connexion distante)**.
   
    e. Dans la page de boîte de dialogue **Configurer l’authentification unique sur TalentLMS** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Remote sign-out URL (URL de déconnexion distante)**.
   
    f. Dans la zone de texte **TargetedID**, entrez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   
    g. Dans la zone de texte **First Name (Prénom)**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   
    h. Dans la zone de texte **Last Name (Nom)**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   
    i. Dans la zone de texte **Email (Adresse e-mail)**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   
    j. Cliquez sur **Enregistrer**.

9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à TalentLMS, les utilisateurs d’Azure AD doivent être approvisionnés dans TalentLMS.  
Dans le cas de TalentLMS, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **TalentLMS** .

2. Cliquez sur **Users (Utilisateurs)**, puis sur **Add User (Ajouter un utilisateur)**.

3. Dans la boîte de dialogue **Add user** , procédez comme suit :
   
    ![Ajouter un utilisateur](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Ajouter un utilisateur")
   
    a. Tapez les valeurs d’attribut associées du compte d’utilisateur Azure AD dans les zones de texte suivantes : **First name (Prénom)**, **Last name (Nom)**, **Email address (Adresse e-mail)**.
   
    b. Cliquez sur **Add User**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par TalentLMS, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Pour affecter des utilisateurs à TalentLMS, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **TalentLMS**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


