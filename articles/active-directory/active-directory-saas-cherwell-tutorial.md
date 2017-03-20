---
title: "Didacticiel : Intégration d’Azure Active Directory à Cherwell | Microsoft Docs"
description: "Apprenez à utiliser Cherwell avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 43acab94bfde65793c4ff944cb6db5dc5de47feb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Didacticiel : Intégration d’Azure Active Directory à Cherwell
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Cherwell. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Cherwell pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Cherwell pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Cherwell (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Cherwell
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scénario")

## <a name="enable-the-application-integration-for-cherwell"></a>Activer l’intégration d’applications pour Cherwell
Cette section décrit l’activation de l’intégration d’applications pour Cherwell.

**Pour activer l’intégration d’applications pour Cherwell, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Cherwell**.
   
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")
7. Dans le volet de résultats, sélectionnez **Cherwell**, puis cliquez sur **Terminer** pour ajouter l’application.
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Cherwell avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Cherwell**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurer l’authentification unique")
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Cherwell**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application** , procédez comme suit :
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurer l’URL de l’application")
  1. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application **Cherwell** (par exemple, *https://\<company name\>.cherwellondemand.com/cherwellclient*). 
  2.  Cliquez sur **Suivant**.
4. Dans la page **Configurer l’authentification unique sur Cherwell** , procédez comme suit :
   
   ![Configurer l’authentification unique](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurer l’authentification unique")
  1.  Cliquez sur **Télécharger le certificat**, puis enregistrez le certificat en local sur votre ordinateur.
  2.  Copiez l’ **URL du fournisseur d’identité**.
  3.  Copiez l’ **URL du service d’authentification unique**.
  4.  Cliquez sur **Suivant**.
5. Envoyez le certificat téléchargé, **l’URL du fournisseur d’identité** et **l’URL du service d’authentification unique** à votre équipe de support technique Cherwell.
   
   >[!NOTE]
   >L’équipe de support technique Cherwell doit se charger de la configuration de l’authentification unique. Vous recevrez une notification dès que l’authentification unique aura été activée pour votre abonnement.
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
  ![Configurer l’authentification unique](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurer l’authentification unique")

## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur
Pour permettre aux utilisateurs Azure AD de se connecter à Cherwell, vous devez les approvisionner dans Cherwell.

En l’occurrence, les comptes d’utilisateur doivent être créés par l’équipe de support technique Cherwell.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Cherwell pour approvisionner des comptes d’utilisateur Azure Active Directory.
>  

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Cherwell, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **Cherwell**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


