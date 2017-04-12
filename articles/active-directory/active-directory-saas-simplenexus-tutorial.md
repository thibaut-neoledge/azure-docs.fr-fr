---
title: "Didacticiel : intégration d’Azure Active Directory à SimpleNexus | Microsoft Docs"
description: "Découvrez comment utiliser SimpleNexus avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89821a05-88e2-4579-b144-0123b2b9cb95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: da3b1e506e603b1f5e3660f31c6d939d77cd2938
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Didacticiel : Intégration d’Azure AD à SimpleNexus
L’objectif de ce didacticiel est de montrer comment intégrer Azure et SimpleNexus.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement SimpleNexus pour lequel l’authentification unique (SSO) est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SimpleNexus pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise SimpleNexus (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour SimpleNexus
2. Configuration de l’authentification unique (SSO) 
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scénario")

## <a name="enabling-the-application-integration-for-simplenexus"></a>Activation de l’intégration d’applications pour SimpleNexus
Cette section décrit l’activation de l’intégration d’applications pour SimpleNexus.

**Pour activer l’intégration d’application pour SimpleNexus, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **simple nexus**.
   
    ![Galerie d’applications](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galerie d’applications")

7. Dans le volet de résultats, sélectionnez **SimpleNexus**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Nexus simple](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Nexus Simple")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur SimpleNexus avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans la page d’intégration d’application **SimpleNexus** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SimpleNexus**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurer l’authentification unique")

3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à SimpleNexus**, tapez votre URL au format *https://simplenexus.com/NomEntreprise\_login*, puis cliquez sur **Suivant**.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur SimpleNexus**, cliquez sur **Télécharger les métadonnées**, puis transférez le fichier de métadonnées à l’équipe de support SimpleNexus.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurer l’authentification unique")
   
    >[!NOTE]
    >L’authentification unique doit être activée par l’équipe de support SimpleNexus. 
    > 

5. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour se connecter à SimpleNexus, les utilisateurs d’Azure AD doivent être approvisionnés dans SimpleNexus.

Dans le cas de SimpleNexus, l’approvisionnement est une tâche manuelle effectuée par l’administrateur du locataire.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par SimpleNexus, pour approvisionner des comptes d’utilisateur AAD. 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à SimpleNexus, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’application **SimpleNexus**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


