---
title: "Didacticiel : Intégration d’Azure Active Directory à SmarterU | Microsoft Docs"
description: "Découvrez comment utiliser SmarterU avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 6b6419694f20f50658d3c0c6de4c22e4a752278e
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Didacticiel : Intégration d’Azure AD à SmarterU
L’objectif de ce didacticiel est de montrer comment intégrer Azure et SmarterU.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire SmarterU

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SmarterU pourront se connecter à l’application à l’aide de l’authentification unique (SSO) sur votre site d’entreprise SmarterU (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour SmarterU
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scénario")

## <a name="enable-the-application-integration-for-smarteru"></a>Activer l’intégration d’applications pour SmarterU
Cette section décrit l’activation de l’intégration d’applications pour SmarterU.

**Pour activer l’intégration d’applications pour SmarterU, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **SmarterU**.
   
    ![Galerie d’applications](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Galerie d’applications")

7. Dans le volet des résultats, sélectionnez **SmarterU**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur SmarterU avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’application **SmarterU** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SmarterU ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurer l’authentification unique")

3. Dans la page **Configurer l’authentification unique sur SmarterU**, pour télécharger vos métadonnées, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de données en local sous le nom **c:\\SmarterUMetaData.cer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurer l’authentification unique")

4. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise SmarterU en tant qu’administrateur.

5. Dans la barre d’outils située en haut, cliquez sur **Account Settings**.
   
    ![Paramètres de compte](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Paramètres de compte")

6. Dans la page de configuration du compte, procédez comme suit :
   
    ![Autorisation externe](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorisation externe") 
  1. Sélectionnez **Enable External Authorization**.
  2. Dans la section **Master Login Control**, cliquez sur l’onglet **SmarterU**.
  3. Dans la section **User Default Login**, cliquez sur l’onglet **SmarterU**.
  4. Sélectionnez **Enable Okta**.
  5. Copiez le contenu du fichier de métadonnées téléchargé et collez-le dans la zone de texte **Okta Metadata** .
  6. Cliquez sur **Save**.

7. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurer l’authentification unique")

## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur
Pour se connecter à SmarterU, les utilisateurs d’Azure AD doivent être approvisionnés dans SmarterU.

Dans le cas de SmarterU, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **SmarterU** .

2. Accédez à **Users**.

3. Dans la section Users, procédez comme suit :
   
    ![Nouvel utilisateur](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nouvel utilisateur")   
  1. Cliquez sur **+User**.
  2. Saisissez les valeurs d’attribut associées au compte d’utilisateur Azure AD dans les zones de texte **Primary Email**, **Employee ID**, **Password**, **Verify Password**, **Given Name** et **Surname**.
  3. Cliquez sur **Active**. 
  4. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SmarterU, pour approvisionner des comptes utilisateur AAD.
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à SmarterU, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.

2. Sur la page d’intégration d’applications **SmarterU**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


