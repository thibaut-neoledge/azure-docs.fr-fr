---
title: "Didacticiel : Intégration d’Azure Active Directory à XMatters OnDemand | Microsoft Docs"
description: "Découvrez comment utiliser XMatters OnDemand avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 143e4a856a31a44a9ec909d07d5f24710a6e2803


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Didacticiel : Intégration d’Azure AD à xMatters OnDemand
L’objectif de ce didacticiel est de montrer comment intégrer Azure et xMatters OnDemand. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un client xMatters OnDemand

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à xMatters OnDemand pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise xMatters OnDemand (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour xMatters OnDemand
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Activation de l’intégration d’applications pour xMatters OnDemand
Cette section décrit l’activation de l’intégration d’applications pour xMatters OnDemand.

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour xMatters OnDemand, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Add application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6. Dans la **zone de recherche**, tapez **xMatters OnDemand**.
   
    ![Galerie d’applications](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Application gallery")

7. Dans le volet de résultats, sélectionnez **xMatters OnDemand**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur XMatters OnDemand avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’applications **xMatters OnDemand**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à xMatters OnDemand ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3. Dans la page **Configurer l’URL de l’application** , procédez comme suit :
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configure app URL")
   
    a. Dans la zone de texte **URL de connexion de xMatters OnDemand**, tapez votre URL au format suivant : `https://<tenant-name>.XMattersOnDemandapp.com`
   
    b. Cliquez sur **Suivant**.

4. Dans la page **Configurer l’authentification unique sur XMatters OnDemand**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **c:\\xMatters OnDemand.cer**.
   
    > [!IMPORTANT]
    > Vous devez transférer le certificat à l’équipe du support technique xMatters. L’équipe du support technique xMatters doit charger le certificat avant que vous ne puissiez finaliser la configuration de l’authentification unique.
    > 
    > 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configure single sign on")

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise XMatters OnDemand en tant qu’administrateur.

6. Dans la barre d’outils de la partie supérieure, cliquez sur **Admin**, puis sur **Company Details** dans la barre de navigation située à gauche.
   
    ![Admin](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7. Dans la page **SAML Configuration** , procédez comme suit :
   
    ![SAML Configuration](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML configuration")
   
    a. Sélectionnez **Enable SAML**.
   
    b. Dans la page **Configurer l’authentification unique sur xMatters OnDemand** du portail Azure Classic, copiez la valeur **ID du fournisseur d’identité**, puis collez-la dans la zone de texte **Identity Provider ID**.
   
    c. Dans la page **Configurer l’authentification unique sur xMatters OnDemand** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Single Sign On URL**.
   
    d. Dans la page **Configurer l’authentification unique sur xMatters OnDemand** du portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Single Logout URL**.
   
    e. Dans la partie supérieure de la page Company Details, cliquez sur **Save Changes**.
    
    ![Détails sur l’entreprise](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configure single sign on")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour pouvoir se connecter à XMatters OnDemand, les utilisateurs d’Azure AD doivent être approvisionnés dans XMatters OnDemand.  
Dans le cas de XMatters OnDemand, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre client **xMatters OnDemand** .

2. Cliquez sur l’onglet **Users** .

3. Cliquez sur **Add User**.
  
    ![Users](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4. Sélectionnez **Active**.

5. Dans la section **Add a User** , procédez comme suit :
   
    ![Add a User](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")
   
    a. Entrez les attributs **UserID**, **First name**, **Last name** et **Site** d’un compte Azure AD valide que vous voulez approvisionner.
    
    b. Cliquez sur **Enregistrer**.


## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Pour affecter des utilisateurs à XMatters OnDemand, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’applications **xMatters OnDemand**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assign users")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


