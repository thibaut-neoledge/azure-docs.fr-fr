---
title: "Didacticiel : intégration d’Azure Active Directory à Panopto | Microsoft Docs"
description: "Apprenez à utiliser Panopto avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 41e04425a291c9f89bc58341c6bad22ad1bdb8ef
ms.openlocfilehash: 90f962fbd19f125cfb609e439f157c4822acfce4


---

# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Didacticiel : Intégration d’Azure Active Directory à Panopto
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Panopto. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Panopto

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Panopto pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Panopto (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Panopto
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-panopto-tutorial/IC777665.png "Scénario")

## <a name="enabling-the-application-integration-for-panopto"></a>Activation de l’intégration d’application pour Panopto
Cette section décrit l’activation de l’intégration d’application pour Panopto.

**Pour activer l’intégration d’application pour Panopto, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-panopto-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-panopto-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Panopto**.
   
   ![Galerie d’applications](./media/active-directory-saas-panopto-tutorial/IC777666.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Panopto**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Panopto avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  

Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Panopto**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Panopto**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Panopto**, tapez votre URL selon le modèle suivant : *https://\<nom-client\>. Panopto.com*, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Panopto**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Panopto en tant qu’administrateur.
6. Dans la barre d’outils située sur la gauche, cliquez sur **System (Système)**, puis sur **Identity Providers (Fournisseurs d’identité)**.
   
   ![Système](./media/active-directory-saas-panopto-tutorial/IC777670.png "système")
7. Cliquez sur **Add Provider**.
   
   ![Fournisseurs d’identité](./media/active-directory-saas-panopto-tutorial/IC777671.png "fournisseurs d’identité")
8. Dans la section du fournisseur SAML, procédez comme suit :
   
   ![Configuration SaaS](./media/active-directory-saas-panopto-tutorial/IC777672.png "configuration SaaS")
   
   1. Dans la liste **Provider Type (Type de fournisseur)**, sélectionnez **SAML20**.
   2. Dans la zone de texte **Instance Name** , attribuez un nom à votre instance.
   3. Dans la zone de texte **Friendly Description** , entrez une description conviviale.
   4. Dans la page **Configurer l’authentification unique sur Panopto** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer (Émetteur)**.
   5. Dans la page **Configurer l’authentification unique sur Panopto** du portail Azure Classic, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Bounce Page Url (URL de la page de rebonds)**.
   6. Créez un fichier **codé en base 64** à partir du certificat téléchargé.    
   
      >[!TIP]
      >Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).
      >
      
   7. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Public Key** .
   8. Cliquez sur **Save**.

 ![Enregistrer](./media/active-directory-saas-panopto-tutorial/IC777673.png "enregistrer")
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurer l’authentification unique")
   
## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Panopto.  
Lorsqu’un utilisateur tente de se connecter à Panopto à l’aide du panneau d’accès, Panopto vérifie si cet utilisateur existe.  

Si aucun compte d’utilisateur n’est disponible, Panopto le crée automatiquement.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Panopto, pour approvisionner des comptes utilisateur AAD.
> 


## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Panopto, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Panopto**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-panopto-tutorial/IC777675.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-panopto-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


