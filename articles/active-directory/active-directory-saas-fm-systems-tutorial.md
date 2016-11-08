---
title: 'Didacticiel : Intégration d’Azure Active Directory à FM:Systems | Microsoft Docs'
description: Apprenez à utiliser FM:Systems avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory à FM:Systems
L’objectif de ce didacticiel est de montrer comment intégrer Azure et FM:Systems. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement FM: Systems pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à FM:Systems pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise FM:Systems (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour FM:Systems
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scénario")

## Activation de l’intégration d’application pour FM:Systems
Cette section décrit l’activation de l’intégration d’application pour FM:Systems.

### Pour activer l’application de l’intégration pour FM:Systems, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **FM:Systems**.
   
   ![Galerie d’applications](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **FM:Systems**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![FM: Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: Systems")
   
   ## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur FM:Systems avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’applications **FM:Systems**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à FM:Systems**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, procédez comme suit :
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurer l’URL de l’application")
   
   1. Dans la zone de texte **FM:Systems Sign On URL**, entrez l’URL de réponse **Reply URL** de FM:Systems (par exemple : *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).
      
      > [!WARNING]
      > La valeur vous est fournie par l’équipe de support technique FM:Systems.
      > 
      > 
   2. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur FM:Systems**, cliquez sur **Télécharger les métadonnées**, puis enregistrez les métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurer l’authentification unique")
5. Envoyez le fichier de métadonnées téléchargé à votre équipe de support technique FM:Systems.
   
   > [!NOTE]
   > Votre équipe de support technique FM:Systems doit se charger de la configuration de l’authentification unique. Vous recevrez une notification lorsque l’authentification unique aura été activée pour votre abonnement.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurer l’authentification unique")
   
   ## Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à FM:Systems, vous devez les approvisionner dans FM:Systems. Dans le cas de FM:Systems, l’approvisionnement est une tâche manuelle.

### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise FM:Systems en tant qu’administrateur.
2. Accédez à **System Administration > Manage Security > Users > User list**.
   
   ![Administration système](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administration système")
3. Cliquez sur **Create new user**.
   
   ![Créer un nouvel utilisateur](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Créer un nouvel utilisateur")
4. Dans la section **Create User**, procédez comme suit :
   
   ![Créer un utilisateur](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Créer un utilisateur")
   
   1. Tapez le nom d’utilisateur, le mot de passe et sa confirmation, l’adresse électronique et l’ID d’employé d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
   2. Cliquez sur **Next**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur, fourni par FM:Systems, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à FM:Systems, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **FM:Systems**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->