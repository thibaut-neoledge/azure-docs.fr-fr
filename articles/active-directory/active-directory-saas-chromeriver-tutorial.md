---
title: "Didacticiel : Intégration d’Azure Active Directory à Chromeriver | Microsoft Docs"
description: "Apprenez à utiliser Chromeriver avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 445c5600-e340-4724-a9cb-3cfaf5770b70
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 88b450c61d382c3c3509f85a8015edc58e8d5139


---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Didacticiel : Intégration d’Azure Active Directory à Chromeriver
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Chromeriver.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Chromeriver pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Chromeriver pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Chromeriver (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Chromeriver
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scenario")

## <a name="enabling-the-application-integration-for-chromeriver"></a>Activation de l’intégration d’applications pour Chromeriver
Cette section décrit l’activation de l’intégration d’applications pour Chromeriver.

### <a name="to-enable-the-application-integration-for-chromeriver-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Chromeriver, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Chromeriver**.
   
   ![Galerie d’applications](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **Chromeriver**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Chromeriver avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’applications **Chromeriver**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Chromeriver**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configure Single Sign-On")
3. Dans la page **Configurer les paramètres de l’application** , procédez comme suit :
   
   ![Configurer les paramètres d’application](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configure App Settings")
   
   1. Dans la zone de texte **URL de réponse**, tapez votre **AssertionConsumerService URL** Chromeriver (par exemple : *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).  
      
      > [!NOTE]
      > L’équipe de support technique Chromeriver peut vous fournir cette valeur.
      > 
      > 
   2. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur Chromeriver**, pour télécharger vos métadonnées, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configure Single Sign-On")
5. Envoyez le fichier de métadonnées téléchargé à l’équipe de support technique Chromeriver.
   
   > [!NOTE]
   > L’équipe de support technique Chromeriver doit se charger de la configuration de l’authentification unique.  
   > Vous recevrez une notification dès que l’authentification unique aura été activée pour votre abonnement.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Chromeriver, vous devez les approvisionner dans Chromeriver.  
En l’occurrence, les comptes d’utilisateur doivent être créés par l’équipe de support technique Chromeriver.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Chromeriver pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-chromeriver-perform-the-following-steps"></a>Pour affecter des utilisateurs à Chromeriver, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Chromeriver**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


