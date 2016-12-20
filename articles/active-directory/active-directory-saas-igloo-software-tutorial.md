---
title: "Didacticiel : Intégration d’Azure Active Directory avec Igloo Software | Microsoft Docs"
description: "Apprenez à utiliser Igloo Software avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/20/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c9e5dfa98e47c6a7a4e34f008fabd1f63b93937c


---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Didacticiel : Intégration d’Azure Active Directory avec Igloo Software
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Igloo Software.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement [Igloo Software](http://www.igloosoftware.com/) pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Igloo Software pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Igloo Software (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Igloo Software
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scenario")

## <a name="enabling-the-application-integration-for-igloo-software"></a>Activation de l’intégration d’application pour Igloo Software
Cette section décrit l’activation de l’intégration d’application pour Igloo Software.

### <a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Igloo Software, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, saisissez **Igloo Software**.
   
   ![Galerie d’applications](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Igloo Software**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Igloo Software avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64 sur votre locataire Central Desktop.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Sur la page d’intégration d’applications **Igloo Software** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configure Single Sign-On")
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Igloo Software**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Authentification unique Microsoft Azure AD](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")
3. Sur la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Igloo Software**, saisissez votre URL selon le modèle « *https://company.igloocommunities.com/?signin* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configure App URL")
4. Sur la page **Configurer l’authentification unique sur Igloo Software**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Igloo Software en tant qu’administrateur.
6. Accédez à **Control panel**.
   
   ![Control panel](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Control Panel")
7. Sous l’onglet **Membership** (Appartenance), cliquez sur **Paramètres de connexion**.
   
   ![Sign In Settings](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Sign in Settings")
8. Dans la section SAML Configuration, cliquez sur **Configure SAML Authentication**.
   
   ![Configuration SAML](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML Configuration")
9. Dans la section **General Configuration** , procédez comme suit :
   
   ![General Configuration](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "General Configuration")
   
   1. Dans la zone de texte **Connection Name** , entrez un nom personnalisé pour votre configuration.
   2. Sur la page **Configurer l’authentification unique sur Igloo Software** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion IdP**.
   3. Sur la page **Configurer l’authentification unique sur Igloo Software** du portail Azure Classic, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **URL de déconnexion IdP**.
   4. Pour **Logout Response and Request HTTP Type** (Type de réponse de déconnexion et de requête HTTP), sélectionnez **POST**.
   5. Créez un fichier texte à partir du certificat téléchargé.
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Supprimez la première ligne et la dernière ligne de la version fichier texte de votre certificat, copiez le texte restant du certificat et collez-le dans la zone de texte **Public Certificate** .
10. Dans **Response and Authentication Configuration**, procédez comme suit :
    
    ![Response and Authentication Configuration](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Response and Authentication Configuration")
    
    1. Pour **Fournisseur d’identité**, sélectionnez **Microsoft ADFS**.
    2. Pour **Type d’identificateur**, sélectionnez **Adresse de messagerie**.
    3. Dans la zone de texte **Email Attribute**, entrez **emailaddress**.
    4. Dans la zone de texte **Attribut de prénom**, saisissez **givenname**.
    5. Dans la zone de texte **Attribut de nom**, saisissez **surname**.
11. Pour terminer la configuration, procédez comme suit :
    
    ![Création d’utilisateurs à l’authentification](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "User creation on Sign in")
    
    1. Pour **Création d’utilisateurs à l’authentification**, sélectionnez **Create a new user in your site when they sign in** (Créer un nouvel utilisateur sur votre site au moment de la connexion).
    2. Pour **Paramètres de connexion**, sélectionnez **Use SAML button on “Sign in” screen** (Utiliser le bouton SAML sur l’écran de connexion).
    3. Cliquez sur **Save**.
12. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Igloo Software.  
Lorsqu’un utilisateur tente de se connecter à Igloo Software à l’aide du panneau d’accès, Igloo Software vérifie si cet utilisateur existe.  
Si aucun compte d’utilisateur n’est disponible, Igloo Software le crée automatiquement.

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Pour affecter des utilisateurs à Igloo Software, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **Igloo Software**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


