---
title: "Didacticiel : Intégration d’Azure Active Directory à Gigya | Microsoft Docs"
description: "Apprenez à utiliser Gigya avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 21372b8554901594f6770ef1ad18467801ef5fea
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Didacticiel : Intégration d’Azure Active Directory à Gigya
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Gigya.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Gigya pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Gigya pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Gigya (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Gigya
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurer l’authentification unique")

## <a name="enabling-the-application-integration-for-gigya"></a>Activation de l’intégration d’application pour Gigya
Cette section décrit l’activation de l’intégration d’application pour Gigya.

### <a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Pour activer l’intégration d’application pour Gigya, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-gigya-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-gigya-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, tapez **Gigya**.
   
    ![Galerie d’applications](./media/active-directory-saas-gigya-tutorial/IC789513.png "Galerie d’applications")

7. Dans le volet de résultats, sélectionnez **Gigya**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
   
## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Gigya avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans la page d’intégration d’application **Gigya** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Gigya**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurer l’authentification unique")

3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion Gigya**, tapez votre URL selon le modèle suivant *http://company.gigya.com*, puis cliquez sur **Suivant**.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur Gigya**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Gigya en tant qu’administrateur.

6. Accédez à **Settings \> SAML Login**, puis cliquez sur le bouton **Add**.
   
    ![Connexion SAML](./media/active-directory-saas-gigya-tutorial/IC789532.png "Connexion SAML")

7. Dans la section **SAML Login** , procédez comme suit :
   
    ![Configuration SAML](./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuration SAML")
   
    a. Dans la zone de texte **Name** , tapez le nom de votre configuration.
   
    b. Dans le portail Azure Classic, sur la page de boîte de dialogue **Configurer l’authentification unique sur Gigya**, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer** (Émetteur).
   
    c. Dans le portail Azure Classic, sur la page de boîte de dialogue **Configurer l’authentification unique sur Gigya**, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL du service d’authentification unique**.
   
    d. Dans le portail Azure Classic, sur la page de boîte de dialogue **Configurer l’authentification unique sur Gigya**, copiez la valeur **Format de l’identificateur du nom**, puis collez-la dans la zone de texte **Name ID Format** (Format d’ID de nom).
   
    e. Créez un fichier **codé en base 64** à partir du certificat téléchargé.
      
    > [!TIP]
    > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    f. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate** .
   
    g. Cliquez sur **Save Settings**.

8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurer l’authentification unique")
   
## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Les utilisateurs Azure AD sont approvisionnés automatiquement la première fois qu’ils se connectent à Gigya.


## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Pour affecter des utilisateurs à Gigya, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration de l’application **Gigya**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-gigya-tutorial/IC789537.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-gigya-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


