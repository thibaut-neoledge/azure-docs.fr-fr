---
title: "Didacticiel : Intégration d’Azure Active Directory à Overdrive Books | Microsoft Docs"
description: "Apprenez à utiliser Overdrive Books avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5d6f44f868010d50b36a9171b7db63aeee7e8bc0
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Didacticiel : Intégration d’Azure Active Directory avec Overdrive Books
L'objectif de ce didacticiel est de montrer comment intégrer Azure et OverDrive.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement OverDrive pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à OverDrive pourront s’authentifier de manière unique (SSO) dans l’application sur votre site d’entreprise OverDrive (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l'intégration d'applications pour OverDrive
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scénario")

## <a name="enable-the-application-integration-for-overdrive"></a>Activer l’intégration d’applications pour OverDrive
Cette section décrit l'activation de l'intégration de l'application pour OverDrive.

**Pour activer l'intégration d’applications pour OverDrive, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **OverDrive**.
   
   ![Galerie d’applications](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **OverDrive**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s'authentifier sur OverDrive avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **OverDrive**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Activer l’authentification unique](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "activer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à OverDrive ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de OverDrive**, tapez votre URL au format « *http://mslibrarytest.libraryreserve.com*, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurer l’URL de l’application")
4. Sur la page **Configurer l’authentification unique sur OverDrive** , téléchargez le fichier de métadonnées, puis envoyez-le à l’équipe du support technique OverDrive.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurer l’authentification unique")
   
   >[!NOTE]
   >L'équipe du support technique OverDrive configure l'authentification unique pour vous et vous envoie une notification lorsque la configuration est terminée.
   > 
   > 
5. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans OverDrive.  

Lorsqu'un utilisateur tente de se connecter à OverDrive, un compte OverDrive est, au besoin, automatiquement créé.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par OverDrive pour approvisionner des comptes d’utilisateur Azure Active Directory.
>
>
 
## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à OverDrive, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **OverDrive**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Affecter des utilisateurs")
   
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

