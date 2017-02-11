---
title: "Didacticiel : Intégration d’Azure Active Directory avec Lynda.com | Microsoft Docs"
description: "Apprenez à utiliser Lynda.com avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68ac46371849282e4d68b581373b3510a2980744


---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Didacticiel : Intégration d’Azure Active Directory à Lynda.com
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Lynda.com.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Lynda.com

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Lynda.com pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Lynda.com (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Lynda.com
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario")

## <a name="enabling-the-application-integration-for-lyndacom"></a>Activation de l’intégration d’applications pour Lynda.com
Cette section décrit l'activation de l'intégration de l'application pour Lynda.com.

### <a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Pour activer l'intégration d’applications pour Lynda.com, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-lynda-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-lynda-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Lynda.com**.
   
   ![Galerie d’applications](./media/active-directory-saas-lynda-tutorial/IC777524.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Lynda.com**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s'authentifier sur Lynda.com avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

> [!IMPORTANT]
> Pour être en mesure de configurer l'authentification unique sur votre locataire Lynda.com, vous devez au préalable contacter le support technique Lynda.com pour faire activer cette fonctionnalité.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic puis dans la page d’intégration d’applications **Lynda.com**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Lynda.com**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configure single sign-on")
3. Sur la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Lynda.com** , tapez l’URL de votre client Lynda.com (par exemple : *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configure app URL")
4. Dans la page **Configurer l’authentification unique sur Lynda.com**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configure single sign-on")
5. Envoyez le fichier de métadonnées téléchargé à l’équipe de support technique Lynda.com. L'équipe de support technique Lynda.com effectue la configuration de l'authentification unique pour vous.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lynda.com.  
Lorsqu'un utilisateur tente de se connecter à Lynda.com à l'aide du panneau d'accès, Lynda.com vérifie si cet utilisateur existe.  
Si aucun compte d'utilisateur n’est disponible, Lynda.com le crée automatiquement.

> [!NOTE]
> Vous pouvez utiliser n'importe quel outil ou API de création de compte utilisateur, fourni par Lynda.com, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Lynda.com, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Lynda.com**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-lynda-tutorial/IC777531.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-lynda-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


