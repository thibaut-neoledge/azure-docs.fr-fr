---
title: "Didacticiel : Intégration d’Azure Active Directory à ScreenSteps | Microsoft Docs"
description: "Découvrez comment utiliser ScreenSteps avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Didacticiel : Intégration d’Azure AD à ScreenSteps
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ScreenSteps.
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire ScreenSteps

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à ScreenSteps pourront se connecter à l’application à l’aide de l’authentification unique sur votre site d’entreprise ScreenSteps (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour ScreenSteps
2. Configuration de l’authentification unique (SSO) 
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scénario")

## <a name="enable-the-application-integration-for-screensteps"></a>Activer l’intégration d’applications pour ScreenSteps
Cette section décrit l’activation de l’intégration d’applications pour ScreenSteps.

**Pour activer l’intégration d’applications pour ScreenSteps, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter une application](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **ScreenSteps**.

    ![Galerie d’applications](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **ScreenSteps**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ScreenSteps avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **ScreenSteps** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ScreenSteps**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurer l’authentification unique")

3. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise ScreenSteps en tant qu’administrateur.

4. Cliquez sur **Account Management**.

    ![Account management](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

5. Cliquez sur **Single Sign-on**.

    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")

6. Cliquez sur **Créer un point de terminaison d’authentification unique**.

    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")

7. Dans la section **Créer un point de terminaison d’authentification unique**, procédez comme suit :

    ![Create an authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")

    1. Dans la zone de texte **Title** , tapez un titre.
    2. Dans la liste **Mode**, sélectionnez **SAML**.
    3. Cliquez sur **Create**.

8. Modifiez le nouveau point de terminaison.

    ![Modifier un point de terminaison](./media/active-directory-saas-screensteps-tutorial/IC778528.png "Modifier un point de terminaison")

9. Dans la section **Edit Single Sign-on Endpoint (Modifier un point de terminaison d’authentification unique)**, procédez comme suit :

    ![Remote authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1. Copiez l’**URL du consommateur SAML** dans le Presse-papiers.

10. Dans la page **Configurer l’URL de l’application** du portail Azure Classic, dans la zone de texte **URL de connexion ScreenSteps**, collez l’**URL du consommateur SAML**, puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurer l’URL de l’application")

11. Dans la page **Configurer l’authentification unique sur ScreenSteps**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurer l’authentification unique")


12. Revenez à la section **Edit Single Sign-on Endpoint (Modifier un point de terminaison d’authentification unique)**, puis procédez comme suit :

    ![Remote authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1. Cliquez sur **Upload new SAML Certificate file** (Charger le nouveau certificat SAML), puis chargez le certificat téléchargé.
    2. Dans la page **Configurer l’authentification unique sur ScreenSteps** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Remote Login URL**.
    3. Dans la page **Configurer l’authentification unique sur ScreenSteps** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Log out URL**.
    4. Sélectionnez un **groupe** auquel attribuer des utilisateurs lorsque ces derniers sont approvisionnés.
    5. Cliquez sur **Update**.
    6. Revenez à **Edit Single Sign-on Endpoint (Modifier un point de terminaison d’authentification unique)**.
    7. Cliquez sur le bouton **Make default for account** (Configurer par défaut pour le compte) pour utiliser ce point de terminaison pour tous les utilisateurs qui se connectent à ScreenSteps. Vous pouvez également cliquer sur le bouton **Ajouter au site** pour utiliser ce point de terminaison pour des sites spécifiques dans **ScreenSteps**.


12. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurer l’authentification unique")

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs



## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à ScreenSteps, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **ScreenSteps**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.


Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


