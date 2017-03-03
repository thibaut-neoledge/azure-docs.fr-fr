---
title: "Didacticiel : Intégration d’Azure Active Directory avec Envoy | Microsoft Docs"
description: "Apprenez à utiliser Envoy avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 913f0688d757829771a8456b3e3be13f69c5b1da
ms.openlocfilehash: a72feeb919f2bbc1d96736e33c8c99e340ff6914
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Didacticiel : Intégration d’Azure Active Directory avec Envoy
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Envoy.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Envoy

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Envoy pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Envoy (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

* Activation de l’intégration d’application pour Envoy
* Configuration de l’authentification unique (SSO)
* Configuration de l'approvisionnement des utilisateurs
* Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scénario")

## <a name="enable-the-application-integration-for-envoy"></a>Activer l’intégration d’applications pour Envoy
Cette section décrit l'activation de l'intégration d’applications pour Envoy.

**Pour activer l’intégration d’applications pour Envoy, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-envoy-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-envoy-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **Envoy**.
   
   ![Galerie d’applications](./media/active-directory-saas-envoy-tutorial/IC776760.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **Envoy**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Envoy avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  

La configuration de l’authentification unique pour Envoy vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **Envoy** du Portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Activer l’authentification unique](./media/active-directory-saas-envoy-tutorial/IC776778.png "activer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Envoy**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Envoy**, tapez votre URL selon le modèle suivant « *https://\<nom_locataire\>.Envoy.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Envoy**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sous **c:\\Envoy.cer**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Envoy en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Settings**.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. Cliquez sur **Company**.
   
   ![Company](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")
8. Cliquez sur **SAML**.
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. Dans la section de configuration **SAML Authentication** , procédez comme suit :
   
   ![SAML authentication](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")   
   >[!NOTE]
   >La valeur de l’ID d’emplacement du siège est générée automatiquement par l’application. 
   > 
   
   1. Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Fingerprint**.  
   
      >[!TIP]
      >Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI). 
      > 
   2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Envoy Suite**, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Identity Provider HTTP SAML URL**.
   3. Cliquez sur **Save changes**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configurer l’authentification unique")
    
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Envoy.  
Lorsqu’un utilisateur tente de se connecter à Envoy à l’aide du panneau d’accès, Envoy vérifie si cet utilisateur existe.  

* Si aucun compte d'utilisateur n’est disponible, Envoy le crée automatiquement.

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Pour affecter des utilisateurs à Envoy, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **Envoy**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-envoy-tutorial/IC776787.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-envoy-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


