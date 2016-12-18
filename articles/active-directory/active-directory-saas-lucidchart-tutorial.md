---
title: "Didacticiel : Intégration d’Azure Active Directory avec Lucidchart | Microsoft Docs"
description: "Apprenez à utiliser Lucidchart avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8386497b457f63ba0b62f50301ce3948ce060b97


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Didacticiel : Intégration d’Azure Active Directory à Lucidchart
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Lucidchart.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Lucidchart pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Lucidchart pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Lucidchart (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Lucidchart
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")

## <a name="enabling-the-application-integration-for-lucidchart"></a>Activation de l’intégration d’application pour Lucidchart
Cette section décrit l’activation de l’intégration d’application pour Lucidchart.

### <a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Lucidchart, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Lucidchart**.
   
   ![Galerie d’applications](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Lucidchart**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Lucidchart avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Lucidchart**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Lucidchart**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Lucidchart**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Lucidchart (par exemple, « *https://chart2.office.lucidchart.com/saml/sso/azure* »), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Lucidchart**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de données localement sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Lucidchart en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Team**.
   
   ![Équipe](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")
7. Cliquez sur **Application \> Manage SAML**.
   
   ![Gérer SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")
8. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :
   
   1. Sélectionnez **Enable SAML Authentication**, puis cliquez sur **Optional**.
      ![SAML Authentication Settings](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
   2. Dans la zone de texte **Domain**, entrez votre domaine, puis cliquez sur **Change Certificate**.
      ![Change Certificate](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
   3. Ouvrez votre fichier de métadonnées téléchargé, copiez son contenu, puis collez-le dans la zone de texte **Upload Metadata** .
      ![Upload Metadata](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
   4. Sélectionnez **Automatically Add new user to the team**, puis cliquez sur **Save changes**.
      ![Save Changes](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")
9. Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lucidchart.  
Lorsqu’un utilisateur tente de se connecter à Lucidchart à l’aide du panneau d’accès, Lucidchart vérifie si cet utilisateur existe.  
Si aucun compte d’utilisateur n’est disponible, Lucidchart le crée automatiquement.

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Pour affecter des utilisateurs à Lucidchart, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Lucidchart**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


