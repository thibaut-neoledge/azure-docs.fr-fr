---
title: "Didacticiel : Intégration d’Azure Active Directory à Brightspace by Desire2Learn | Microsoft Docs"
description: "Apprenez à utiliser Brightspace by Desire2Learn avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: cdd7a42f12c145327249ff7c03ede9a465374e35
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Didacticiel : Intégration d’Azure Active Directory à Brightspace by Desire2Learn
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Brightspace by Desire2Learn.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Brightspace by Desire2Learn pour lequel l’authentification unique (SSO) est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Brightspace by Desire2Learn pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Brightspace by Desire2Learn (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

* Activation de l’intégration d’applications pour Brightspace by Desire2Learn
* Configuration de l’authentification unique (SSO)
* Configuration de l'approvisionnement des utilisateurs
* Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scénario")

## <a name="enable-the-application-integration-for-brightspace-by-desire2learn"></a>Activer l’intégration d’applications pour Brightspace by Desire2Learn
Cette section décrit l’activation de l’intégration d’applications pour Brightspace by Desire2Learn.

**Pour activer l’intégration d’applications pour Brightspace by Desire2Learn, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Brightspace by Desire2Learn**.
   
   ![Galerie d’applications](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Brightspace by Desire2Learn**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Brightspace by Desire2Learn avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Brightspace by Desire2Learn**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Brightspace by Desire2Learn**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application** , procédez comme suit :
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurer l’URL de l’application")   
   1. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à **Brightspace by Desire2Learn** (par exemple : *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
   2. Cliquez sur **Suivant**.
4. Dans la page **Configurer l’authentification unique sur Brightspace by Desire2Learn**, pour télécharger vos métadonnées, cliquez sur **Télécharger les métadonnées**, puis enregistrez les métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurer l’authentification unique")
5. Envoyez le fichier de métadonnées téléchargé à l’équipe de support technique Brightspace by Desire2Learn.
   
   >[!NOTE]
   >L’équipe de support technique Brightspace by Desire2Learn doit se charger de la configuration de l’authentification unique.
   >Vous recevrez une notification dès que l’authentification unique aura été activée pour votre abonnement.
   > 
 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour permettre aux utilisateurs Azure AD de se connecter à Brightspace by Desire2Learn, vous devez les approvisionner dans Brightspace by Desire2Learn.  

En l’occurrence, les comptes d’utilisateur doivent être créés par l’équipe de support technique Brightspace by Desire2Learn.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Brightspace by Desire2Learn pour approvisionner des comptes d’utilisateur Azure Active Directory. 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Brightspace by Desire2Learn, suivez les étapes ci-dessous :**
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Brightspace by Desire2Learn**, cliquez sur **Affecter des utilisateurs**.
   
  ![Affecter des utilisateurs](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
  ![Oui](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


