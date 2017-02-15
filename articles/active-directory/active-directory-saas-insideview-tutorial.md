---
title: "Didacticiel : Intégration d’Azure Active Directory avec InsideView | Microsoft Docs"
description: "Apprenez à utiliser InsideView avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0da7c8f20fb6bbe78294aab2fb69c24a2dddd877


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Didacticiel : Intégration d’Azure Active Directory à InsideView
L’objectif de ce didacticiel est de montrer comment intégrer Azure et InsideView.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire InsideView

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à InsideView pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise InsideView (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour InsideView
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")

## <a name="enabling-the-application-integration-for-insideview"></a>Activation de l’intégration d’application pour InsideView
Cette section décrit l’activation de l’intégration d’application pour InsideView.

### <a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Pour activer l’intégration d’application pour InsideView, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **InsideView**.
   
   ![Galerie d’applications](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **InsideView**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur InsideView avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **InsideView**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à InsideView**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de réponse InsideView**, tapez votre URL d’authentification InsideView (par exemple, `https://my.insideview.com/iv/<STS Name>/login.iv`), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur InsideView**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise InsideView en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Admin**, **SingleSignOn Settings**, puis sur **Add SAML**.
   
   ![Paramètres d’authentification unique SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. Dans la section **Add a New SAML** , procédez comme suit :
   
   ![Add a New SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. Dans la zone de texte **STS Name** , attribuez un nom à votre configuration.
   2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur InsideView**, copiez la valeur **Point de terminaison initié du fournisseur du services**, puis collez-la dans la zone de texte **SamlP/WS-Fed Unsolicated EndPoint**.
   3. Créez un fichier **codé en base 64** à partir du certificat téléchargé.
      
      > [!TIP]
      > Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **STS Certificate** .
   5. Dans la zone de texte **Crm User Id Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   6. Dans la zone de texte **Crm Email Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   7. Dans la zone de texte **Crm First Name Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   8. Dans la zone de texte **Crm lastName Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Cliquez sur **Save**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à InsideView, vous devez les approvisionner dans InsideView.  
Dans le cas d’InsideView, l’approvisionnement est une tâche manuelle.

Pour obtenir des utilisateurs ou des contacts créés dans InsideView, contactez votre responsable de réussite client ou envoyez un courrier électronique à **support@insideview.com**

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par InsideView, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Pour affecter des utilisateurs à InsideView, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **InsideView**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


