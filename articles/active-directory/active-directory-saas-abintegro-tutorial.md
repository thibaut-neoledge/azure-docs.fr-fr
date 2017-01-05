---
title: "Didacticiel : Intégration d’Azure Active Directory à Abintegro | Microsoft Docs"
description: "Apprenez à utiliser Abintegro avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 99287e1f-4189-494a-97c8-e1c03d047fd3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 64a7ff2552d2ccc97b1ec90cf524aed6b62ecb82


---
# <a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Didacticiel : Intégration d’Azure Active Directory à Abintegro
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Abintegro.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Abintegro pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Abintegro pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Abintegro (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Abintegro
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scénario")

## <a name="enabling-the-application-integration-for-abintegro"></a>Activation de l’intégration d’application pour Abintegro
Cette section décrit l’activation de l’intégration d'application pour Abintegro.

### <a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Abintegro, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **abintegro**.
   
   ![Galerie d’applications](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Abintegro**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Abintegro avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Abintegro**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Abintegro ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion Abintegro**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Abintegro (par ex., `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Abintegro**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurer l’authentification unique")
5. Envoyez le fichier de métadonnées à l’équipe de support Abintegro.
   
   > [!NOTE]
   > La configuration de l’authentification unique doit être effectuée par l’équipe de support Abintegro. Vous recevez une notification dès qu’elle est terminée.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurer l’authentification unique")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Abintegro.  
Quand un utilisateur affecté tente de se connecter à Abintegro via le volet d’accès, Abintegro vérifie si cet utilisateur existe.  
Si aucun compte d’utilisateur n’est encore disponible, Abintegro le crée automatiquement.

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Pour affecter des utilisateurs à Abintegro, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Abintegro**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


