---
title: "Didacticiel : Intégration d’Azure Active Directory avec Learningpool | Microsoft Docs"
description: "Apprenez à utiliser Learningpool avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 52fec971875bb797b5de679918528c5c472e4182


---
# <a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Didacticiel : Intégration d’Azure Active Directory à Learningpool
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Learningpool.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Learningpool pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Learningpool pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Learningpool (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Learningpool
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-learningpool"></a>Activation de l’intégration d’application pour Learningpool
Cette section décrit l’activation de l’intégration d’application pour Learningpool.

### <a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Learningpool, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Learningpool**.
   
   ![Galerie d’applications](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")
7. Dans le volet des résultats, sélectionnez **Learningpool**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Learningpool avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

Votre application Learningpool attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML** .  
La capture d’écran suivante montre un exemple :

![Attributs du jeton SAML](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **Learningpool**, dans le menu en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.
   
   ![Attributs](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")
2. Pour ajouter les mappages d’attribut requis, procédez comme suit :
   
   ### 
   | Nom de l'attribut | Valeur de l’attribut |
   | --- | --- |
   |  | |
   
   | urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname |
   | --- | --- |
   |  urn:oid:2.5.4.42 |User.givenname |
   | urn:oid:0.9.2342.19200300.100.1.3 |User.mail |
   | urn:oid:2.5.4.4 |User.surname |
   
   1. Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut utilisateur**.
   2. Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut pour cette ligne.
   3. Dans la liste **Valeur de l’attribut** , sélectionnez la valeur d’attribut pour cette ligne.
   4. Cliquez sur **Terminé**.
3. Cliquez sur **Appliquer les modifications**.
4. Dans votre navigateur, cliquez sur **Précédent** pour rouvrir la boîte de dialogue **Démarrage rapide**.
5. Cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")
6. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Learningpool**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")
7. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Learningpool**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Learningpool (par exemple, https://parliament.preview.learningpool.com/auth/shibboleth/index.php), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")
8. Dans la page **Configurer l’authentification unique sur Learningpool**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")
9. Transférez ce fichier de métadonnées à l’équipe de support de Learningpool.
   
   > [!NOTE]
   > L’authentification unique doit être activée par l’équipe de support de Learningpool.
   > 
   > 
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Learningpool, vous devez les approvisionner dans Learningpool.

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Learningpool.  
Les utilisateurs doivent être créés par votre équipe de support technique Learningpool.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Learningpool, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Pour affecter des utilisateurs à Learningpool, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Learningpool**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


