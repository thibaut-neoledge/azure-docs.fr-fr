---
title: "Didacticiel : Intégration d’Azure Active Directory à Workday | Microsoft Docs"
description: "Découvrez comment utiliser Workday avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e6b1c8cbf45fea4f5af16231003ccc07a2a53233
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Didacticiel : Intégration d’Azure Active Directory à Workday
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Workday. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire dans Workday

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Workday
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Configuration de l'approvisionnement des utilisateurs

![Scénario](./media/active-directory-saas-workday-tutorial/IC782919.png "Scénario")

## <a name="enabling-the-application-integration-for-workday"></a>Activation de l’intégration d’application pour Workday
Cette section décrit l’activation de l’intégration d’application pour Workday.

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Workday, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4. Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis sur **Ajouter une application utilisable par mon organisation**.
   
    ![Que souhaitez-vous faire ?](./media/active-directory-saas-workday-tutorial/IC700995.png "Que souhaitez-vous faire ?")

5. Dans la **zone de recherche**, entrez **Workday**.
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6. Dans le volet de résultats, sélectionnez **Workday**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Workday avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Workday**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782920.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Workday**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782921.png "Configurer l’authentification unique")

3. Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant**.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-workday-tutorial/IC782957.png "Configurer l’URL de l’application")
   
    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Workday au format suivant : `https://impl.workday.com/<tenant>/login-saml2.htmld`
   
    b.  Dans la zone de texte **URL de réponse de Workday**, tapez l’URL appropriée au format suivant : `https://impl.workday.com/<tenant>/login-saml.htmld`
   
    > [!NOTE]
    > Votre URL de réponse doit disposer d'un sous-domaine (par exemple, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Quelque chose comme « *http://www.myworkday.com* » fonctionnera, mais pas « *http://myworkday.com* ». 
    > 
    > 

4. Dans la page **Configurer l’authentification unique sur Workday**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782922.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Workday en tant qu’administrateur.

6. Accédez à **Menu \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7. Accédez à **Administration des comptes**.
   
    ![Administration des comptes](./media/active-directory-saas-workday-tutorial/IC782924.png "Administration des comptes")

8. Accédez à **Edit Tenant Setup – Security**.
   
    ![Modifier la sécurité du locataire](./media/active-directory-saas-workday-tutorial/IC782925.png "Modifier la sécurité du locataire")

9. Dans la section **Redirection URLs** , procédez comme suit :
   
    ![URL de redirection](./media/active-directory-saas-workday-tutorial/IC7829581.png "URL de redirection")
   
    a. Cliquez sur le **signe plus**pour ajouter une ligne.
   
    b. Dans les zones de texte **URL de redirection de connexion** et **URL de redirection mobile**, tapez l’**URL de locataire Workday** que vous avez entrée dans la page **Configurer l’URL de l’application** du portail Azure Classic.
   
    c. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique dans Workday** de la boîte de dialogue, copiez l’**URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de redirection de déconnexion**.
   
    d.  Dans la zone de texte **Environment** , tapez le nom de l’environnement.  

    >[!NOTE]
    > La valeur de l’attribut Environment est liée à celle de l’URL du client :  
    >-   Si le nom de domaine de l’URL du client Workday commence par impl (par ex. : *https://impl.workday.com/\<client\>/login-saml2.htmld*), l’attribut **Environment** doit être défini sur Implementation.  
    >-   Si le nom de domaine commence par autre chose, vous devez contacter Workday pour obtenir la valeur correspondante de l’attribut **Environment** .

1. Dans la section **SAML Setup** , procédez comme suit :
   
    ![Configuration de SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configuration de SAML")
   
    a.  Sélectionnez **Enable SAML Authentication**.
   
    b.  Cliquez sur le **signe plus**pour ajouter une ligne.

2. Dans la section SAML Identity Providers, procédez comme suit :
   
    ![Fournisseurs d’identité SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Fournisseurs d’identité SAML")
   
    a. Dans la zone de texte Identity Provider Name, saisissez le nom d’un fournisseur (par ex., *SPInitiatedSSO*).
   
    b. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Workday**, copiez la valeur de l’**ID de fournisseur d’identité**, puis collez-la dans la zone de texte **Émetteur**.
   
    c. Sélectionnez **Enable Workday Initiated Logout**.
   
    d. Dans la page **Configurer l’authentification unique dans Workday** du portail Azure Classic, copiez la valeur de l’**URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de demande de déconnexion**.

    e. Cliquez sur **Certificat de clé publique du fournisseur d’identité**, puis sur **Créer**. 

    ![Créer](./media/active-directory-saas-workday-tutorial/IC782928.png "créer")

    f. Cliquez sur **Créer la clé publique x509**. 

    ![Créer](./media/active-directory-saas-workday-tutorial/IC782929.png "créer")


1. Dans la section **Afficher la clé publique x509** , procédez comme suit : 
   
    ![Afficher la clé publique x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Afficher la clé publique x509") 
   
    a. Dans la zone de texte **Name**, tapez le nom du certificat (par ex. : *PPE\_SP*).
   
    b. Dans la zone de texte **Valid From** , tapez la valeur correspondante du certificat.
   
    c.  Dans la zone de texte **Valid To** , tapez la valeur correspondante du certificat.
   
    > [!NOTE]
    > Vous pouvez obtenir les dates de début et de fin de validité du certificat téléchargé en double-cliquant dessus.  Les dates sont répertoriées sous l’onglet **Details** .
    > 
    > 
   
    d. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
   
    > [!TIP]
    > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e.  Ouvrez le certificat codé en base 64 dans le Bloc-notes, puis copiez son contenu.
   
    f.  Dans la zone de texte **Certificat** , collez le contenu du Presse-papiers.
   
    g.  Cliquez sur **OK**.

2. Procédez comme suit : 
   
    ![Configuration SSO](./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuration SSO")
   
    a.  Activez **x509 Private Key Pair**.
   
    b.  Dans la zone de texte **Service Provider ID**, tapez **http://www.workday.com**.
   
    c.  Sélectionnez **Enable SP Initiated SAML Authentication**.
   
    d.  Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Workday**, copiez la valeur de l’**URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL du service IdP SSO**.
   
    e. Sélectionnez **Ne pas compresser la demande d’authentification initiée par le fournisseur de services**.
   
    f. Comme **Méthode de signature de la demande d’authentification**, sélectionnez **SHA256**. 
   
    ![Méthode de signature de demande la d’authentification](./media/active-directory-saas-workday-tutorial/IC782932.png "Méthode de signature de la demande d’authentification") 
   
    g. Cliquez sur **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

3. Dans la page **Configurer l’authentification unique sur Workday** du portail Azure Classic, cliquez sur **Suivant**. 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782934.png "Configurer l’authentification unique")

4. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour approvisionner un utilisateur test dans Workday, vous devez contacter l’équipe du support technique Workday.  
L’équipe du support technique Workday crée l’utilisateur à votre place.

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-workday-perform-the-following-steps"></a>Pour affecter des utilisateurs à Workday, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **Workday**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-workday-tutorial/IC782935.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-workday-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


