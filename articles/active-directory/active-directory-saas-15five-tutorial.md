---
title: 'Didacticiel : Intégration d’Azure Active Directory à 15Five | Microsoft Docs'
description: Apprenez à utiliser 15Five avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory à 15Five
L’objectif de ce didacticiel est de montrer comment intégrer Azure et 15Five. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement 15Five pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à 15Five pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise 15Five (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour 15Five
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-15five-tutorial/IC784667.png "Scénario")

## Activation de l’intégration d’application pour 15Five
Cette section décrit l’activation de l’intégration d’application pour 15Five.

### Pour activer l’intégration d’application pour 15Five, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-15five-tutorial/IC749321.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-15five-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **15Five**.
   
   ![Galerie d’applications](./media/active-directory-saas-15five-tutorial/IC784668.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **15Five**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
   ## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur 15Five avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### Pour configurer l’authentification unique, procédez comme suit :
1. Sur la page d’intégration d’applications **15Five** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-15five-tutorial/IC784670.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à 15Five**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-15five-tutorial/IC784671.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de 15Five**, tapez votre URL selon le modèle « *https://company.15Five.com*", puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-15five-tutorial/IC784672.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur 15Five**, cliquez sur **Télécharger les métadonnées**, puis transférez le fichier de métadonnées à l’équipe de support 15Five.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-15five-tutorial/IC784673.png "Configurer l’authentification unique")
   
   > [!NOTE]
   > L’authentification unique doit être activée par l’équipe de support 15Five.
   > 
   > 
5. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-15five-tutorial/IC784674.png "Configurer l’authentification unique")
   
   ## Configuration de l'approvisionnement des utilisateurs

Pour se connecter à 15Five, les utilisateurs d’Azure AD doivent être approvisionnés dans 15Five. Dans le cas de 15Five, l’approvisionnement est une tâche manuelle.

### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à votre site d’entreprise **15Five** en tant qu’administrateur.
2. Accédez à **Manage Company**.
   
   ![Manage Company](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")
3. Accédez à **People > Add People**.
   
   ![People](./media/active-directory-saas-15five-tutorial/IC784676.png "People")
4. Dans la section Add New Person, procédez comme suit :
   
   ![Add New Person](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")
   
   1. Tapez le prénom, le nom, la fonction et l’adresse de messagerie du compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **First Name**, **Last Name**, **Title** et **Email address**.
   2. Cliquez sur **Done**.
   
   > [!NOTE]
   > Le titulaire du compte Azure AD reçoit alors un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur 15Five fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.
> 
> 

## Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à 15Five, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **15Five**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-15five-tutorial/IC784678.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-15five-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->