---
title: "Didacticiel : intégration d’Azure Active Directory à Sugar CRM | Microsoft Docs"
description: "Découvrez comment utiliser Sugar CRM avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: f0e857dfa915d98beeeb06b224fbe761943dfbce


---
# <a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Didacticiel : Intégration d’Azure AD à Sugar CRM
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Sugar CRM.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Sugar CRM pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Sugar CRM pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Sugar CRM (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Sugar CRM
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scénario")

## <a name="enabling-the-application-integration-for-sugar-crm"></a>Activation de l’intégration d’applications pour Sugar CRM
Cette section décrit l’activation de l’intégration d’applications pour Sugar CRM.

### <a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Sugar CRM, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **Sugar CRM**.
   
    ![Galerie d’applications](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Galerie d’applications")

7. Dans le volet de résultats, sélectionnez **Sugar CRM**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Sugar CRM avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base&64; sur votre locataire Sugar CRM.  
Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Sugar CRM** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Sugar CRM**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configurer l’authentification unique")

3. Dans la zone de texte **URL de connexion à Sugar CRM** de la page **Configurer l’URL de l’application**, tapez l’URL entrée par vos utilisateurs pour se connecter à votre application Sugar CRM (ex. : *http://company.sugarondemand.com*), puis cliquez sur **Suivant**.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur Sugar CRM**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Sugar CRM en tant qu’administrateur.

6. Accédez à **Admin**.
   
    ![Administrateur](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administrateur")

7. Dans la section **Administration**, cliquez sur **Password Management (Gestion des mots de passe)**.
   
    ![Administration](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")

8. Sélectionnez **Enable SAML Authentication**.
   
    ![Administration](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")

9. Dans la section **SAML Authentication** , procédez comme suit :
   
    ![Authentification SAML](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Authentification SAML")
   
    a. Dans la page de boîte de dialogue **Configurer l’authentification unique sur Sugar CRM** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Login URL (URL de connexion)**.
   
    b. Dans la page de boîte de dialogue **Configurer l’authentification unique sur Sugar CRM** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **SLO URL (URL SLO)**.
   
    c. Créez un fichier **codé en base 64** à partir du certificat téléchargé.
      
    > [!TIP]
    > Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    d. Ouvrez votre certificat codé en base&64; dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate** .
   
    e. Cliquez sur **Enregistrer**.

10. Dans la page de boîte de dialogue **Configurer l’authentification unique sur Sugar CRM** du portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à Sugar CRM, les utilisateurs d’Azure AD doivent être approvisionnés dans Sugar CRM.  
Dans le cas de Sugar CRM, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **Sugar CRM** en tant qu’administrateur.

2. Accédez à **Admin**.
   
    ![Administrateur](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administrateur")

3. Dans la section **Administration**, cliquez sur **User Management (Gestion des utilisateurs)**.
   
    ![Administration](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")

4. Accédez à **Users (Utilisateurs) \> Create New User (Créer un utilisateur)**.
   
    ![Créer un nouvel utilisateur](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Créer un nouvel utilisateur")

5. Dans l’onglet **User Profile** , procédez comme suit :
   
    ![Nouvel utilisateur](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Nouvel utilisateur")
   
    a. Indiquez le prénom, le nom et l’adresse de messagerie du compte Azure AD valide que vous souhaitez approvisionner, dans les zones de texte correspondantes.

6. Pour **Status (Statut)**, sélectionnez **Active (Actif)**.

7. Dans l’onglet Password, procédez comme suit :
   
    ![Nouvel utilisateur](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Nouvel utilisateur")
   
    a. Tapez le mot de passe dans la zone de texte correspondante.
   
    b. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Sugar CRM, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>Pour affecter des utilisateurs à SpringCM, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **Sugar CRM**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO2-->


