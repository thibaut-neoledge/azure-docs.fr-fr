---
title: "Didacticiel : Intégration d’Azure Active Directory à SumoLogic | Microsoft Docs"
description: "Découvrez comment utiliser SumoLogic avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 040974883f96ac05fccd38d933090ed4aa932e95
ms.lasthandoff: 12/08/2016


---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Didacticiel : Intégration d’Azure Active Directory à SumoLogic
L’objectif de ce didacticiel est de montrer comment intégrer Azure et SumoLogic.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire SumoLogic

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SumoLogic pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise SumoLogic (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour SumoLogic
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scénario")

## <a name="enabling-the-application-integration-for-sumologic"></a>Activation de l’intégration d’applications pour SumoLogic
Cette section décrit l’activation de l’intégration d’applications pour SumoLogic.

### <a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour SumoLogic, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **sumologic**.
   
    ![Galerie d’applications](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galerie d’applications")

7. Dans le volet des résultats, sélectionnez **SumoLogic**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur SumoLogic avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base&64; sur votre locataire SumoLogic.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **SumoLogic** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SumoLogic**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurer l’authentification unique")

3. Dans la zone de texte **URL de connexion à SumoLogic** de la page **Configuration l’URL de l’application**, tapez votre URL au format « *https://\<nom_locataire\>.SumoLogic.com* », puis cliquez sur **Suivant**.
   
    ![Configurer une URL](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurer une URL")

4. Dans la page **Configurer l’authentification unique sur SumoLogic**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise SumoLogic en tant qu’administrateur.

6. Accédez à **Manage \> Security**.
   
    ![Gestion](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gestion")

7. Cliquez sur **SAML**.
   
    ![Paramètres de sécurité globaux](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Paramètres de sécurité globaux")

8. Dans la liste **Select a configuration or create a new one**, sélectionnez **Azure AD**, puis cliquez sur **Configure**.
   
    ![Configurer SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurer SAML 2.0")

9. Dans la boîte de dialogue **Configure SAML 2.0** , procédez comme suit :
   
    ![Configurer SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurer SAML 2.0")
   
    a. Dans la zone de texte **Configuration Name**, entrez **Azure AD**.
    
    b. Sélectionnez **Debug Mode**.
   
    c. Dans la page de boîte de dialogue **Configurer l’authentification unique sur SumoLogic** du portail Azure Classic, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **Issuer** (Émetteur).
   
    d. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur SumoLogic**, copiez la valeur **URL de la demande d’authentification** et collez-la dans la zone de texte **Authn Request URL** (URL de la demande d’authentification).
   
    e. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
    > [!TIP]
    > Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    f. Ouvrez votre certificat codé en base&64; dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .
   
    g. Dans **Email Attribute**, sélectionnez **Use SAML subject**.
   
    h. Sélectionnez **SP initiated Login Configuration**.
   
    i. Dans la zone de texte **Login Path**, entrez **Azure**.
   
    j. Cliquez sur **Enregistrer**.

10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique sur SumoLogic**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à SumoLogic, les utilisateurs d’Azure AD doivent être approvisionnés dans SumoLogic.  
Dans le cas de SumoLogic, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **SumoLogic** .

2. Accédez à **Gérer \> Utilisateurs**.
   
    ![Utilisateurs](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Utilisateurs")

3. Cliquez sur **Add**.
   
    ![Utilisateurs](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Utilisateurs")

4. Dans la boîte de dialogue **New User** , procédez comme suit :
   
    ![Nouvel utilisateur](./media/active-directory-saas-sumologic-tutorial/IC778563.png "Nouvel utilisateur")
   
    a. Indiquez le prénom, le nom et l’adresse e-mail du compte Azure AD valide que vous souhaitez approvisionner, dans les zones de texte **First Name**, **Last Name** et **Email** .
   
    b. Sélectionnez un rôle
   
    c. Pour **Status (Statut)**, sélectionnez **Active (Actif)**.
   
    d. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SumoLogic, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Pour affecter des utilisateurs à SumoLogic, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **SumoLogic**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


