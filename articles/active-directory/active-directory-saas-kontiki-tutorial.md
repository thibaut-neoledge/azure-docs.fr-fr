---
title: "Didacticiel : Intégration d’Azure Active Directory avec Kontiki | Microsoft Docs"
description: "Apprenez à utiliser Kontiki avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 38b7f9772d5cf4e7b7861ef57804436de2a2f442


---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Didacticiel : Intégration d’Azure Active Directory à Kontiki
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Kontiki.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Kontiki pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Kontiki pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Kontiki (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l'intégration d'applications pour Kontiki
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenario")

## <a name="enabling-the-application-integration-for-kontiki"></a>Activation de l'intégration d'applications pour Kontiki
Cette section décrit l'activation de l’intégration d’applications pour Kontiki.

### <a name="to-enable-the-application-integration-for-kontiki-perform-the-following-steps"></a>Pour activer l'intégration d’applications pour Kontiki, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Kontiki**.
   
   ![Galerie d’applications](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Kontiki**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s'authentifier sur Kontiki avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Kontiki**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configure Single SignOn")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Kontiki**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configure Single SignOn")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Kontiki**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Kontiki (par ex. *https://company.mc.eval.kontiki.com/*), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Kontiki**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configure Single SignOn")
5. Envoyez le fichier de métadonnées à l’équipe du support technique Kontiki.
   
   > [!NOTE]
   > La configuration de l’authentification unique doit être effectuée par l’équipe du support technique Kontiki. Vous recevez une notification dès qu’elle est terminée.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Kontiki.  
Lorsqu'un utilisateur tente de se connecter à Kontiki à l'aide du panneau d'accès, Kontiki vérifie si cet utilisateur existe.  
Si aucun compte d'utilisateur n’est disponible, Kontiki le crée automatiquement.

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-kontiki-perform-the-following-steps"></a>Pour affecter des utilisateurs à Kontiki, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Kontiki**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


