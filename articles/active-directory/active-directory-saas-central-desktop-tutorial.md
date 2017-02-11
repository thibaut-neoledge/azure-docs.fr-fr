---
title: "Didacticiel : Intégration d’Azure Active Directory à Central Desktop | Microsoft Docs"
description: "Apprenez à utiliser Central Desktop avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5923bf1012c862d500d3c9b0062f0d7662e9f1bc


---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Didacticiel : Intégration d’Azure Active Directory à Central Desktop
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Central Desktop. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Central Desktop pour lequel l’authentification unique est activée / locataire Central Desktop

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Central Desktop
2. Configuration de l’authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enabling-the-application-integration-for-central-desktop"></a>Activation de l’intégration d’applications pour Central Desktop
Cette section décrit l’activation de l’intégration d’applications pour Central Desktop.

### <a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Central Desktop, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Central Desktop**.
   
   ![Galerie d’applications](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application gallery")
7. Dans le volet de résultats, sélectionnez **Central Desktop**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Central Desktop avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 sur votre locataire Central Desktop.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Central Desktop**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Central Desktop**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")
3. Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant** : 
   
   * Dans la zone de texte **URL de connexion à Central Desktop**, tapez l’URL de votre locataire Central Desktop (par exemple, *http://contoso.centraldesktop.com*).
   * Dans la zone de texte URL de réponse Central Desktop, tapez votre URL AssertionConsumerService Central Desktop (par exemple, https://contoso.centraldesktop.com/saml2-assertion.php).
   
   > [!NOTE]
   > Vous pouvez obtenir la valeur à partir des métadonnées de Central Desktop (par exemple, *http://contoso.centraldesktop.com*).
   > 
   > 
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")
4. Dans la page **Configurer l’authentification unique sur Central Desktop**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")
5. Connectez-vous à votre locataire **Central Desktop** .
6. Accédez à **Settings**, cliquez sur **Advanced**, puis sur **Single Sign On**.
   
   ![Paramétrage - Avancé](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")
7. Dans la page **Single Sign On Settings** , procédez comme suit :
   
   ![Single Sign On Settings](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
   1. Sélectionnez **Activer l’authentification unique SAMLv2**.
   2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Central Desktop**, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **SSO URL**.
   3. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Central Desktop**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **SSO Login URL**.
   4. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Central Desktop**, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **SSO Logout URL**.
8. Dans la section **Message Signature Verification Method** , procédez comme suit :
   
   ![Message Signature Verification Method](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
   1. Sélectionnez **Certificate**.
   2. Dans la liste **SSO Certificate**, sélectionnez **RSH SHA256**.
   3. Créez un fichier texte à partir du certificat téléchargé, copiez le contenu du fichier texte et collez-le dans le champ **SSO Certificate** .  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Sélectionnez **Display a link to your SAMLv2 login page**.
9. Cliquez sur **Mettre à jour**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Central Desktop. Cette section explique comment créer des comptes d’utilisateur AAD dans Central Desktop.

### <a name="to-provision-user-accounts-to-central-desktop"></a>Pour approvisionner des comptes d’utilisateur dans Central Desktop :
1. Connectez-vous à votre locataire Central Desktop.
2. Accédez à **People \> Internal Member**.
3. Cliquez sur **Ajouter des membres internes**.
   
   ![Personnes](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")
4. Dans la zone de texte **Email Address of New Members**, tapez un compte AAD à approvisionner, puis cliquez sur **Next**.
   
   ![Adresses de messagerie des nouveaux membres](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. Cliquez sur **Add Internal member(s)**.
   
   ![Ajouter un membre interne](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   > [!NOTE]
   > Les utilisateurs que vous avez ajoutés recevront un message électronique contenant un lien de confirmation sur lequel ils doivent cliquer pour activer le compte.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Central Desktop pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Pour affecter des utilisateurs à Central Desktop, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Central Desktop**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


