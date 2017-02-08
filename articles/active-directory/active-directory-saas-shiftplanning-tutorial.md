---
title: "Didacticiel : Intégration d’Azure Active Directory à ShiftPlanning | Microsoft Azure"
description: "Découvrez comment utiliser ShiftPlanning avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 403f297e7f0bfdde8ade3f35c401bcefb79ea2f5


---
# <a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Didacticiel : Intégration d’Azure AD à ShiftPlanning
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ShiftPlanning.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement ShiftPlanning pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à ShiftPlanning pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ShiftPlanning (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour ShiftPlanning
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")

## <a name="enabling-the-application-integration-for-shiftplanning"></a>Activation de l’intégration d’applications pour ShiftPlanning
Cette section décrit l’activation de l’intégration d’applications pour ShiftPlanning.

### <a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour ShiftPlanning, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **ShiftPlanning**.
   
   ![Galerie d’applications](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **ShiftPlanning**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur ShiftPlanning avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’applications **ShiftPlanning** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à ShiftPlanning**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")
3. Dans la zone de texte **URL de connexion à ShiftPlanning** de la page **Configurer l’URL de l’application**, tapez votre URL au format *https://company.shiftplanning.com/includes/saml/*, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur ShiftPlanning**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **ShiftPlanning** en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")
7. Sous **Integration**, cliquez sur **Single Sign-On**.
   
   ![Authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")
8. Dans la section **Single Sign-On** , procédez comme suit :
   
   ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")
   
   1. Sélectionnez **SAML Enabled**.
   2. Sélectionnez **Allow Password Login**
   3. Dans la page **Configurer l’authentification unique sur ShiftPlanning** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **URL de l’émetteur SAML**.
   4. Dans la page **Configurer l’authentification unique sur ShiftPlanning** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Remote Logout URL**.
   5. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate** .
   7. Cliquez sur **Save Settings**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à ShiftPlanning, les utilisateurs d’Azure AD doivent être approvisionnés dans ShiftPlanning.  
Dans le cas de ShiftPlanning, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous au site d’entreprise **ShiftPlanning** en tant qu’administrateur.
2. Cliquez sur **Admin**.
   
   ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")
3. Cliquez sur **Staff**.
   
   ![Staff](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")
4. Sous **Actions**, cliquez sur **Add employee**.
   
   ![Add employees](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")
5. Dans la section **Add employees** , procédez comme suit :
   
   ![Save Employees](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")
   
   1. Indiquez le prénom, le nom et l’adresse de messagerie du compte AAD valide que vous souhaitez approvisionner dans les zones de texte **First Name**, **Last Name** et **Email**.
   2. Cliquez sur **Save Employees**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte utilisateur, fourni par ScreenSteps, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Pour affecter des utilisateurs à ShiftPlanning, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **ShiftPlanning**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


