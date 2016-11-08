---
title: 'Didacticiel : Intégration d’Azure Active Directory à RightAnswers | Microsoft Docs'
description: Apprenez à utiliser RightAnswers avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-rightanswers"></a>Didacticiel : Intégration d’Azure Active Directory à RightAnswers
L’objectif de ce didacticiel est de montrer comment intégrer Azure et RightAnswers. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement RightAnswers pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à RightAnswers pourront s’authentifier de manière unique dans l’application en s’appuyant sur la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour RightAnswers
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")

## <a name="enabling-the-application-integration-for-rightanswers"></a>Activation de l’intégration d’application pour RightAnswers
Cette section décrit l’activation de l’intégration d’application pour RightAnswers.

### <a name="to-enable-the-application-integration-for-rightanswers,-perform-the-following-steps:"></a>Pour activer l’intégration d’application pour RightAnswers, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **RightAnswers**.
   
   ![Galerie d’applications](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **RightAnswers**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur RightAnswers avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **RightAnswers**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à RightAnswers**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configure Single Sign-On")
3. Dans la page **Configurer les paramètres de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application RightAnswers (par exemple, *https://fortify.rightanswers.com/portal/ss/*), puis cliquez sur **Suivant**.
   
   ![Configurer les paramètres d’application](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configure App Settings")
4. Dans la page **Configurer l’authentification unique sur RightAnswers**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configure Single Sign-On")
5. Envoyez le fichier de métadonnées téléchargé à votre équipe de support technique RightAnswers.
   
   > [!NOTE]
   > Votre équipe de support technique RightAnswers doit se charger de la configuration de l’authentification unique.
   > Vous recevrez une notification dès que l’authentification unique aura été activée pour votre abonnement.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à RightAnswers, vous devez les approvisionner dans RightAnswers.  
Dans le cas de RightAnswers, l’approvisionnement est une tâche automatisée.  
Vous n’avez rien à faire.

Au besoin, les utilisateurs sont automatiquement créés lors de la première tentative d’authentification unique.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par RightAnswers, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-rightanswers,-perform-the-following-steps:"></a>Pour affecter des utilisateurs à RightAnswers, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **RightAnswers**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


