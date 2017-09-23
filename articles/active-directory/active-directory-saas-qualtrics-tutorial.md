---
title: "Didacticiel : Intégration d’Azure Active Directory à Qualtrics | Microsoft Docs"
description: "Apprenez à utiliser Qualtrics avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2fcde595a40dafda7549f5bccb582b57585b314e
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017

---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Didacticiel : Intégration d’Azure Active Directory avec Qualtrics
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Qualtrics.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Qualtrics pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Qualtrics pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Qualtrics (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l'intégration d'applications pour Qualtrics
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scénario")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Activation de l'intégration d'applications pour Qualtrics
Cette section décrit l'activation de l'intégration de l'application pour Qualtrics.

**Pour activer l’intégration d’applications pour Qualtrics, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **Qualtrics**.
   
   ![Galerie d’applications](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Qualtrics**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s'authentifier sur Qualtrics avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Qualtrics**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Qualtrics**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Qualtrics**, tapez votre URL (par exemple : *https://ssotest2ut1.qualtrics.com*), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Qualtrics**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configurer l’authentification unique")
5. Envoyez le fichier de métadonnées à l’équipe du support technique Qualtrics.
   
   >[!NOTE]
   >La configuration de l’authentification unique doit être effectuée par l’équipe du support technique Qualtrics. Vous recevez une notification dès qu’elle est terminée.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Qualtrics. Lorsqu'un utilisateur tente de se connecter à Qualtrics à l'aide du panneau d'accès, Qualtrics vérifie si cet utilisateur existe.  

Si aucun compte d'utilisateur n’est disponible, Qualtrics le crée automatiquement.

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Qualtrics, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Qualtrics**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


