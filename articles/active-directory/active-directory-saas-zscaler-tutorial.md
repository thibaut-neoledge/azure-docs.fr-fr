---
title: "Didacticiel : Intégration d’Azure Active Directory à Zscaler | Microsoft Docs"
description: "Découvrez comment utiliser Zscaler avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f9e6f640b97a35aab84063f17a155a32f809d374
ms.openlocfilehash: 835f2e0b636af17d2c2c032cda572b00a4fdbef7
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Didacticiel : Intégration d’Azure AD à Zscaler
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Zscaler. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un client dans Zscaler

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Zscaler
2. Configuration de l’authentification unique
3. Configuration des paramètres de proxy
4. Configuration de l'approvisionnement des utilisateurs
5. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scénario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Activation de l’intégration d’applications pour Zscaler
Cette section décrit l’activation de l’intégration d’applications pour Zscaler.

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Pour activer l’intégration d’applications pour Zscaler, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, tapez **Zscaler**.
   
    ![Galerie d’applications](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galerie d’applications")

7. Dans le volet de résultats, sélectionnez **Zscaler**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Zscaler avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez charger un certificat vers Zscaler.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Sur la page d’intégration d’applications **Zscaler** du Portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Activer l’authentification unique](./media/active-directory-saas-zscaler-tutorial/IC769229.png "activer l’authentification unique")

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Zscaler**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configurer l’authentification unique")

3. Sur la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Zscaler**, tapez l’URL de connexion que vous avez obtenue de Zscaler, puis cliquez sur **Suivant** : 
   
    > [!NOTE]
    > Contactez l’équipe du support technique Zscaler si vous ne connaissez pas votre URL de connexion.
    > 
    > 
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur Zscaler** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurer l’authentification unique")
   
    1. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **C:\\Zscaler.cer**.
    2. Copiez l’ **URL de demande d’authentification** dans le Presse-papiers.

5. Connectez-vous au client Zscaler.

6. Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.
   
    ![Administration](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7. Sous **Gérer les administrateurs et les rôles**, cliquez sur **Gérer les utilisateurs et l’authentification**.
   
    ![Gérer les administrateurs et les rôles](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Gérer les administrateurs et les rôles")

8. Dans la section **Choose Authentication Option for your Organization** , procédez comme suit :
   
    ![Choisir les options d’authentification](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choisir les options d’authentification")
   
    1. Sélectionnez **Authenticate using SAML Single Sign-On**.
    2. Cliquez sur **Configure SAML Single Sign-On Parameters**.

9. Dans la boîte de dialogue **Configurer les paramètres d’authentification unique SAML**, procédez comme suit, puis cliquez sur **OK** :
   
    ![Charger le certificat](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Charger le certificat")
   
    1. Dans la zone de texte **URL du portail SAML vers lequel les utilisateurs sont renvoyés pour l’authentification**, collez la valeur du champ **URL de la demande d’authentification** à partir du Portail Azure Classic.
   
    2. Dans la zone de texte **Attribut contenant le nom de connexion**, tapez **NameID**.
   
    3. Dans le champ **Upload SSL Public Certificate** (Télécharger le certificat SSL public), chargez le certificat que vous avez téléchargé à partir du portail Azure Classic.
   
    4. Sélectionnez **Enable SAML Auto-Provisioning**.

10. Dans la page **Configure User Authentication** , procédez comme suit :
    
    ![Configurer l’authentification utilisateur](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurer l’authentification utilisateur")
    
    1. Cliquez sur **Save**.
    2. Cliquez sur **Activate Now**.

11. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurer l’authentification unique")


## <a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1. Démarrez **Internet Explorer**.

2. Dans le menu **Outils**, sélectionnez **Options Internet** pour ouvrir la boîte de dialogue **Options Internet**.
   
    ![Options Internet](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Options Internet")

3. Cliquez sur l’onglet **Connexions** .
   
    ![Connexions](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connexions")

4. Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.

5. Dans la section Serveur proxy, procédez comme suit :
   
    ![Serveur proxy](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Serveur proxy")
   
    1. Sélectionnez Utiliser un serveur proxy pour votre réseau local.

    2. Dans la zone de texte Adresse, tapez **gateway.zscalertwo.net**.
   
    3. Dans la zone de texte Port, tapez **80**.
   
    4. Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.
   
    5. Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
Pour pouvoir se connecter à Zscaler, les utilisateurs d’Azure AD doivent être approvisionnés dans Zscaler.  
Dans le cas de Zscaler, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Connectez-vous au locataire **Zscaler** .

2. Cliquez sur **Administration**.

    ![Administration](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3. Cliquez sur **User Management**.
   
    ![Gestion des utilisateurs](./media/active-directory-saas-zscaler-tutorial/IC781036.png "Gestion des utilisateurs")

4. Sous l’onglet **Utilisateurs**, cliquez sur **Ajouter**.
   
    ![Ajouter](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Ajouter")

5. Dans la section Add User, procédez comme suit :
   
    ![Ajouter un utilisateur](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Ajouter un utilisateur")
   
    1. Renseignez les zones de texte **UserID**, **Nom d’affichage de l’utilisateur**, **Mot de passe** et **Confirmer le mot de passe**, puis sélectionnez **Groupes** ainsi que l’attribut **Département** du compte AAD valide que vous souhaitez approvisionner.
   
    2. Cliquez sur **Enregistrer**.

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zscaler, procédez comme suit :

1. Dans le portail Azure Classic, créez un compte de test.

2. Sur la page d’intégration d’applications **Zscaler**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


