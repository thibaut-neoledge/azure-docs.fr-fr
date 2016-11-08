---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Mimecast Personal Portal | Microsoft Docs'
description: Apprenez à utiliser Mimecast Personal Portal avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/08/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory avec Mimecast Personal Portal
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Mimecast Personal Portal. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Mimecast Personal Portal pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Mimecast Personal Portal pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Mimecast Personal Portal (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Mimecast Personal Portal
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scénario")

## Activation de l’intégration d’application pour Mimecast Personal Portal
Cette section décrit l’activation de l’intégration d’application pour Mimecast Personal Portal.

### Pour activer l’intégration d’application pour Mimecast Personal Portal, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **Mimecast Personal Portal**.
   
   ![Galerie d’applications](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **Mimecast Personal Portal**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Portail personnel Mimecast](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Portail personnel Mimecast")
   
   ## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Mimecast Personal Portal avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Mimecast Personal Portal**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Mimecast Personal Portal**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Mimecast Personal Portal**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Mimecast Personal Portal (par exemple : « https://webmail-fr.mimecast.com » ou « https://webmail-be.mimecast.com »), puis cliquez sur **Suivant**.
   
   > [!NOTE]
   > L’URL d’ouverture de session est spécifique à la région.
   > 
   > 
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Mimecast Personal Portal**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Personal Portal en tant qu’administrateur.
6. Accédez à **Services > Application**.
   
   ![Applications](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applications")
7. Cliquez sur **Authentication Profiles**.
   
   ![Profils d’authentification](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Profils d’authentification")
8. Cliquez sur **New Authentication Profile**.
   
   ![Nouveau profil d’authentification](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "Nouveau profil d’authentification")
9. Dans la section **Authentication Profile**, procédez comme suit :
   
   ![Profil d’authentification](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Profil d’authentification")
   
   1. Dans la zone de texte **Description**, indiquez un nom pour votre configuration.
   2. Sélectionnez **Enforce SAML Authentication for Mimecast Personal Portal**.
   3. Pour **Provider**, sélectionnez **Azure Active Directory**.
   4. Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Mimecast Personal Portal** du portail Azure Classic, copiez la valeur de **URL de l’émetteur** et collez-la dans la zone de texte **Issuer URL**.
   5. Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Mimecast Personal Portal** du portail Azure Classic, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Login URL**.
   6. Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Mimecast Personal Portal** du portail Azure Classic, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Logout URL**.
      
      > [!NOTE]
      > Les valeurs d’URL de connexion et de déconnexion sont identiques pour Mimecast Personal Portal.
      > 
      > 
   7. Créez un fichier **codé en base 64** à partir du certificat téléchargé.
      
      > [!TIP]
      > Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).
      > 
      > 
   8. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, supprimez la première (« *--* ») et la dernière ligne (« *--* »), copiez le contenu restant dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)**.
   9. Sélectionnez **Allow Single Sign On**.
   10. Cliquez sur **Enregistrer**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configurer l’authentification unique")
    
    ## Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Personal Portal, vous devez les approvisionner dans Mimecast Personal Portal. Dans le cas de Mimecast Personal Portal, l’approvisionnement est une tâche manuelle.

Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Connectez-vous à **Mimecast Personal Portal** en tant qu’administrateur.
2. Accédez à **Directories > Internal**.
   
   ![Répertoires](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Répertoires")
3. Cliquez sur **Register New Domain**.
   
   ![Enregistrer un nouveau domaine](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Enregistrer un nouveau domaine")
4. Après avoir créé votre domaine, cliquez sur **New Address**.
   
   ![Nouvelle adresse](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "Nouvelle adresse")
5. Dans la boîte de dialogue New Address, procédez comme suit :
   
   ![Enregistrer](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Enregistrer")
   
   1. Tapez l’adresse électronique, le nom global, le mot de passe et sa confirmation pour un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address**, **Global Name**, **Password** et **Confirm Password**.
   2. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur, fourni par Mimecast Personal Portal, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à Mimecast Personal Portal, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Mimecast Personal Portal**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->