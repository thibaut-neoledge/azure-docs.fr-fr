---
title: "Didacticiel : Intégration d’Azure Active Directory avec Jobscience | Microsoft Docs"
description: "Apprenez à utiliser Jobscience avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e4e665ced0fa4e2749c052c2cd087ffb0ee698cb


---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Didacticiel : Intégration d’Azure Active Directory à Jobscience
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Jobscience.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Jobscience pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Jobscience pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Jobscience (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Jobscience
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")

## <a name="enabling-the-application-integration-for-jobscience"></a>Activation de l’intégration d’application pour Jobscience
Cette section décrit l’activation de l’intégration d’application pour Jobscience.

### <a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Jobscience, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Jobscience**.
   
   ![Galerie d’applications](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Jobscience**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Jobscience avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Jobscience oblige à récupérer une valeur d’empreinte numérique dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Connectez-vous au site d’entreprise Jobscience en tant qu’administrateur.
2. Accédez à **Setup**.
   
   ![Paramétrage](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")
3. Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**. 
   
   ![My Domain](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
4. Pour vérifier que votre domaine a été configuré correctement, assurez-vous qu’il figure dans **Step 4 Deployed to Users**, et passez en revue **My Domain Settings**.
   
   ![Domaine déployé pour l’utilisateur](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman Deployed to User")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre portail Azure Classic.
6. Dans la page d’intégration d’application **Jobscience**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configure Single Sign-On")
7. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Jobscience**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configure Single Sign-On")
8. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Jobscience**, tapez votre URL selon le modèle suivant « *http://company.my.salesforce.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configure App URL")
9. Dans la page **Configurer l’authentification unique sur Jobscience**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configure Single Sign-On")
10. Sur le site d’entreprise Jobscience, cliquez sur **Security Controls**, puis sur **Single Sign-On Settings**.
    
    ![Security Controls](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")
11. Dans la section **Single Sign-On Settings** , procédez comme suit :
    
    ![Single Sign-On Settings](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")
    
    1. Sélectionnez **SAML Enabled**.
    2. Cliquez sur **New**.
12. Dans la boîte de dialogue **SAML Single Sign-On Setting Edit** , procédez comme suit :
    
    ![Paramètre d’authentification unique SAML](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")
    
    1. Dans la zone de texte **Name** , tapez le nom de votre configuration.
    2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Jobscience** de la boîte de dialogue, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **Issuer**
    3. Dans la zone de texte **Id d’entité**, saisissez **https://salesforce-jobscience.com**
    4. Cliquez sur **Parcourir** pour charger votre certificat Azure AD.
    5. Pour **SAML Identity Type**, sélectionnez **Assertion contains the Federation ID from the User object**.
    6. Pour **SAML Identity Location**, sélectionnez **Identity is in the NameIdentfier element of the Subject statement**.
    7. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Jobscience**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Identity Provider Login URL**
    8. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Jobscience**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Identity Provider Logout URL**
    9. Cliquez sur **Save**.
13. Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**. 
    
    ![My Domain](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
14. Dans la section **Login Page Branding** de la page **My Domain**, cliquez sur **Edit**.
    
    ![Personnalisation de la page de connexion](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")
15. Le nom des **SAML SSO Settings** s’affiche dans la section **Authentication Service** de la page **Login Page Branding**. Sélectionnez-le, puis cliquez sur **Save**.
    
    ![Personnalisation de la page de connexion](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")
16. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configure Single Sign-On")

Pour obtenir l’URL d’authentification unique initiée par le fournisseur de services , cliquez sur **Single Sign On settings** dans la section **Security Controls**.

![Security Controls](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")

Cliquez sur le profil d’authentification unique créé à l’étape précédente.  
Cette page affiche l’URL d’authentification unique de votre société (par exemple, *https://companyname.my.salesforce.com?so=companyid*).

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour permettre aux utilisateurs Azure AD de se connecter à Jobscience, vous devez les approvisionner dans Jobscience.  
Dans le cas de Jobscience, cet approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous au site d’entreprise **Jobscience** en tant qu’administrateur.
2. Accédez à Setup.
   
   ![Paramétrage](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")
3. Accédez à **Manage Users \> Users**.
   
   ![Utilisateurs](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Users")
4. Cliquez sur **New User**.
   
   ![Tous les utilisateurs](./media/active-directory-saas-jobscience-tutorial/IC784370.png "All Users")
5. Dans la boîte de dialogue **Edit User** , procédez comme suit :
   
   ![Modification de l’utilisateur](./media/active-directory-saas-jobscience-tutorial/IC784371.png "User Edit")
   
   1. Indiquez le prénom, le nom, l’alias, l’adresse de messagerie, le nom d’utilisateur et le surnom de l’utilisateur Azure AD que vous souhaitez approvisionner, dans les zones de texte correspondantes.
   2. Cliquez sur **Save**.
   
   > [!NOTE]
   > Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Jobscience pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Pour affecter des utilisateurs à Jobscience, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Jobscience**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


