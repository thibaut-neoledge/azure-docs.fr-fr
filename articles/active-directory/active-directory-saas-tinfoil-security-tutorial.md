---
title: "Didacticiel : Intégration d’Azure Active Directory à Tinfoil Security | Microsoft Docs"
description: "Découvrez comment utiliser Tinfoil Security avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e3a93cc039d4e24d19f2df2d859c5899cd2a402e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Didacticiel : Intégration d’Azure Active Directory à Tinfoil Security
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Tinfoil Security.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Tinfoil Security pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Tinfoil Security pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Tinfoil Security (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Tinfoil Security
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurer l’authentification unique")

## <a name="enabling-the-application-integration-for-tinfoil-security"></a>Activation de l’intégration d’applications pour Tinfoil Security
Cette section décrit l’activation de l’intégration d’applications pour Tinfoil Security.

### <a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Tinfoil Security, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **Tinfoil Security**.
   
    ![Galerie d’applications](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galerie d’applications")

7. Dans le volet des résultats, sélectionnez **Tinfoil Security**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Tinfoil Security avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Tinfoil Security oblige à récupérer une valeur d’empreinte numérique dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Tinfoil Security** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Tinfoil Security**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurer l’authentification unique")

3. Dans la zone de texte **URL de réponse de Tinfoil Security** de la page **Configurer l’URL de l’application**, entrez l’URL de votre service ACS (Assertion Consumer Service) Tinfoil Security (par exemple, « *https://www.tinfoilsecurity.com/saml/consume* »), puis cliquez sur **Suivant**.
   
    > [!NOTE]
    > Vous devez pouvoir obtenir l’URL d’ACS dans les métadonnées de Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).
    > 
    > 
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur Tinfoil Security**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sous le nom **c:\\Tinfoil Security.cer**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Tinfoil Security en tant qu’administrateur.

6. Dans la barre d’outils située en haut, cliquez sur **My Account**.
   
    ![Tableau de bord](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Tableau de bord")

7. Cliquez sur **Security**.
   
    ![Sécurité](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Sécurité")

8. Dans la page de configuration **Single Sign on** , procédez comme suit :
   
    ![Authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Authentification unique")
   
    a. Sélectionnez **Enable SAML**.
   
    b. Cliquez sur **Manual Configuration**.
   
    c. Dans la boîte de dialogue **Configurer l’authentification unique sur Tinfoil Security** du portail Azure Classic, copiez la valeur de **URL d’authentification unique SAML** et collez-la dans la zone de texte **URL de publication SAML**.
   
    d. Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **SAML Certificate Fingerprint**.  
      
    > [!TIP]
    > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    e. Copiez **Votre ID de compte**.
   
    f. Cliquez sur **Enregistrer**.

9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurer l’authentification unique")

10. Dans le menu situé en haut, cliquez sur **Attributs** to open the **SAML Token Attributs** .
    
    ![Attributs](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributs")

11. Pour ajouter les mappages d’attribut requis, procédez comme suit :
    
    ![Attributs](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributs")
    
    a. Cliquez sur **Ajouter un attribut utilisateur**.

    b. Dans la zone de texte **Nom de l’attribut**, entrez **accountid**.

    c. Dans la zone de texte **Valeur de l’attribut** , collez l’ID de compte que vous avez copié dans la section précédente.

    d. Cliquez sur **Terminé**.

12. Cliquez sur **Appliquer les modifications**.

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour se connecter à Tinfoil Security, les utilisateurs d’Azure AD doivent être approvisionnés dans Tinfoil Security.  
Dans le cas de Tinfoil Security, l’approvisionnement est une tâche manuelle.

### <a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Pour configurer l’approvisionnement des utilisateurs, procédez comme suit :
1. Si l’utilisateur fait partie d’un compte d’entreprise, vous devez contacter l’équipe de support Tinfoil Security pour obtenir le compte d’utilisateur créé.
2. Si l’utilisateur est un utilisateur normal de SaaS Tinfoil Security, l’utilisateur peut ajouter un collaborateur à n’importe quel site de l’utilisateur. Cela déclenche l’envoi à l’adresse de messagerie spécifiée, d’une invitation à créer un compte utilisateur Tinfoil Security.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Tinfoil Security, pour approvisionner des comptes d’utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Pour affecter des utilisateurs à Tinfoil Security, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Tinfoil Security**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


