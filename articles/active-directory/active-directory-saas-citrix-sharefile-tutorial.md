---
title: 'Didacticiel : Intégration d’Azure Active Directory à Citrix ShareFile | Microsoft Docs'
description: Apprenez à utiliser Citrix ShareFile avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
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
# Didacticiel : Intégration d’Azure Active Directory à Citrix ShareFile
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Citrix ShareFile. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Citrix ShareFile

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Citrix ShareFile pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Citrix ShareFile (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Citrix ShareFile
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scénario")

## Activation de l’intégration d’applications pour Citrix ShareFile
Cette section décrit l’activation de l’intégration d’applications pour Citrix ShareFile.

### Pour activer l’intégration d’applications pour Citrix ShareFile, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Citrix ShareFile**.
   
   ![Galerie d’applications](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Citrix ShareFile**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
   ## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Citrix ShareFile avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’applications **Citrix ShareFile**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Activer l’authentification unique](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Activer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Citrix ShareFile**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle `https://<tenant-name>.shareFile.com`, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Citrix ShareFile**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Citrix ShareFile** en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Admin**.
7. Dans le volet de navigation de gauche, sélectionnez **Configure Single Sign-On**.
   
   ![Administration des comptes](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administration des comptes")
8. Dans la page de boîte de dialogue **Single Sign-On/ SAML 2.0 Configuration** sous **Basic Settings**, procédez comme suit :
   
   ![Authentification unique](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Authentification unique")
   
   1. Cliquez sur **Enable SAML**.
   2. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix ShareFile**, copiez la valeur **ID d’entité**, puis collez-la dans la zone de texte **Your IDP Issuer/ Entity ID**.
   3. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix ShareFile**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Login URL**.
   4. Dans le portail Azure Classic, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix ShareFile**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Logout URL**.
   5. Cliquez sur **Change** en regard du champ **X.509 Certificate**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure AD Classic. ![Paramètres de base](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Paramètres de base")
9. Cliquez sur **Save** dans le portail de gestion Citrix ShareFile.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurer l’authentification unique")
    
    ## Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Citrix ShareFile, vous devez les approvisionner dans Citrix ShareFile. En l’occurrence, cet approvisionnement est une tâche manuelle.

### Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **Citrix ShareFile**.
2. Cliquez sur **Manage Users > Manage Users Home > + Create Employee**.
   
   ![Créer un employé](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Créer un employé")
3. Entrez l’**adresse de messagerie**, le **prénom** et le **nom** d’un compte Azure AD valide que vous voulez approvisionner.
   
   ![Informations de base](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informations de base")
4. Cliquez sur **Add User**.
   
   > [!NOTE]
   > Le détenteur du compte AAD recevra un message électronique et suivra un lien pour confirmer le compte avant qu’il ne soit activé.
   > 
   > 

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Citrix ShareFile pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à Citrix ShareFile, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Citrix ShareFile**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->