---
title: "Didacticiel : Intégration d’Azure Active Directory à Aha! | Microsoft Docs"
description: "Apprenez à utiliser Aha! avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f95aecd18d5231413678669d01d16387737e9db2


---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Didacticiel : Intégration d’Azure Active Directory à Aha!
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Aha!.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Aha! pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Aha! pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Aha! (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Aha!
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario")

## <a name="enabling-the-application-integration-for-aha"></a>Activation de l’intégration d’application pour Aha!
Cette section décrit l’activation de l’intégration d’application pour Aha!.

### <a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Aha!, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-aha-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-aha-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Aha!**.
   
   ![Galerie d’applications](./media/active-directory-saas-aha-tutorial/IC798945.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Aha!**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Aha! avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Sur la page d’intégration d’applications **Aha!** Dans la page d’intégration d’applications, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798946.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Aha! ?**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798947.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion Aha!**, tapez l’URL dont se servent vos utilisateurs pour se connecter à votre application Application (par exemple : « *https://company.aha.io/session/new* »), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-aha-tutorial/IC798948.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Aha!**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798949.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Aha! en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Settings**.
   
   ![Settings](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")
7. Cliquez sur **Account**.
   
   ![Profil](./media/active-directory-saas-aha-tutorial/IC798951.png "Profile")
8. Cliquez sur **Security and single sign-on**.
   
   ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")
9. Dans la section **Single Sign-On**, pour **Identity Provider**, sélectionnez **SAML2.0**.
   
   ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")
10. Dans la page de configuration **Single Sign on** , procédez comme suit :
    
    ![Single Sign on](./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")
    
    1. Dans la zone de texte **Name** , tapez le nom de votre configuration.
    2. Pour **Configure using**, sélectionnez **Metadata File**.
    3. Pour charger le fichier de métadonnées téléchargé, cliquez sur **Browse**.
    4. Cliquez sur **Mettre à jour**.
11. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798955.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Aha!, les utilisateurs d’Azure AD doivent être approvisionnés dans Aha!.  
Dans le cas d’Aha!, l’approvisionnement est une tâche automatisée.  
Vous n’avez aucune opération à effectuer.

Au besoin, les utilisateurs sont automatiquement créés lors de la première tentative d’authentification unique.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil de création de compte utilisateur Aha! ou toute API fournie par Aha! pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-aha-perform-the-following-steps"></a>Pour affecter des utilisateurs à Aha!, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Aha!**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-aha-tutorial/IC798956.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-aha-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


