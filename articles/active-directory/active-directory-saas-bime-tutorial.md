---
title: "Didacticiel : Intégration d’Azure Active Directory à Bime | Microsoft Docs"
description: "Apprenez à utiliser Bime avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c1adfe732b88a6a48a3aa9ad3ca7785703667e3d


---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Didacticiel : Intégration d’Azure Active Directory à Bime
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Bime.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Bime

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Bime pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Bime (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Bime
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-bime-tutorial/IC775552.png "Scenario")

## <a name="enabling-the-application-integration-for-bime"></a>Activation de l’intégration d’applications pour Bime
Cette section décrit l’activation de l’intégration d’applications pour Bime.

### <a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Bime, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-bime-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-bime-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-bime-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Bime**.
   
   ![Galerie d’applications](./media/active-directory-saas-bime-tutorial/IC775553.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Bime**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Bime avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Bime vous oblige à récupérer une valeur d’empreinte dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Bime** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC771709.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Bime**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775555.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL d’après le modèle « *https://\<nom_locataire\>.Bimeapp.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-bime-tutorial/IC775556.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Bime**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sous **c:\\Bime.cer**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775557.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Bime en tant qu’administrateur.
6. Dans la barre d’outils, cliquez sur **Admin**, puis sur **Account**.
   
   ![Admin](./media/active-directory-saas-bime-tutorial/IC775558.png "Admin")
7. Dans la page de configuration du compte, procédez comme suit :
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775559.png "Configure Single Sign-On")
   
   1. Sélectionnez **Activer l’authentification SAML**.
   2. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Bime**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Remote Login URL**.
   3. Copiez la valeur **Empreinte** du certificat exporté, puis collez-la dans la zone de texte **Certificate Fingerprint**.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. Cliquez sur **Enregistrer**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Bime, vous devez les approvisionner dans Bime.  
En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre locataire **Bime** .
2. Dans la barre d’outils, cliquez sur **Admin**, puis sur **Users**.
   
   ![Admin](./media/active-directory-saas-bime-tutorial/IC775561.png "Admin")
3. Dans la **Users List**, cliquez sur **Add New User** (« + »).
   
   ![Utilisateurs](./media/active-directory-saas-bime-tutorial/IC775562.png "Users")
4. Dans la page de boîte de dialogue **User Details** , procédez comme suit :
   
   ![User Details](./media/active-directory-saas-bime-tutorial/IC775563.png "User Details")
   
   1. Entrez le prénom, le nom, la connexion et l’adresse de messagerie d’un compte AAD valide que vous voulez approvisionner
   2. Cliquez sur Enregistrer.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bime pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-bime-perform-the-following-steps"></a>Pour affecter des utilisateurs à Bime, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Bime**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-bime-tutorial/IC775564.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-bime-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


