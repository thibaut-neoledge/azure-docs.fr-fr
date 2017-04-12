---
title: "Didacticiel : Intégration d’Azure Active Directory à Sprinklr | Microsoft Docs"
description: "Découvrez comment utiliser Sprinklr avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 44b5314a250d88f7ea2f8db2c1270a9090f083cd
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Didacticiel : Intégration d’Azure Active Directory à Sprinklr
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Sprinklr.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Sprinklr

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Sprinklr pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Sprinklr (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Sprinklr
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scénario")

## <a name="enable-the-application-integration-for-sprinklr"></a>Activer l’intégration d’applications pour Sprinklr
Cette section décrit l’activation de l’intégration d’applications pour Sprinklr.

**Pour activer l’intégration d’applications pour Sprinklr, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Ajouter une application](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Ajouter une application")

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6. Dans la **zone de recherche**, entrez **Sprinklr**.
   
    ![Galerie d’applications](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galerie d’applications")

7. Dans le volet des résultats, sélectionnez **Sprinklr**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Sprinklr avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. 


Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  

Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’application **Sprinklr** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurer l’authentification unique")

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Sprinklr**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurer l’authentification unique")

3. Dans la zone de texte **URL de connexion à Sprinklr** de la page **Configurer l’URL de l’application**, tapez votre URL au format « *https://\<nom_locataire\>.Sprinklr.com* », puis cliquez sur **Suivant**.
   
    ![Configurer l’URL de l’application](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurer l’URL de l’application")

4. Dans la page **Configurer l’authentification unique sur Sprinklr**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurer l’authentification unique")

5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Sprinklr en tant qu’administrateur.

6. Accédez à **Administration \> Settings**.
   
    ![Administration](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7. Accédez à **Manager Partner \> Single Sign** dans le volet gauche.
   
    ![Gérer les partenaires](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gérer les partenaires")

8. Cliquez sur **+Add Single Sign Ons**.
   
    ![Authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Authentification unique")

9. Dans la page **Single Sign on** , procédez comme suit :
   
    ![Authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Authentification unique")
  1. Dans la zone de texte **Name** , indiquez le nom de votre configuration (par exemple, *WAADSSOTest*).
  2. Sélectionnez **Enabled**.
  3. Sélectionnez **Use new SSO Certificate**.
  4. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
  
     >[!TIP]
     >Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o). 
     >    
     
  5. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate (Certificat du fournisseur d’identité)**.
  6. Dans le portail Azure Classic, dans la boîte de dialogue **Configurer l’authentification unique sur Sprinklr** :
     *  Copiez la valeur **ID de fournisseur d’identité**, puis collez-la dans la zone de texte **ID d’entité**.
     * Copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion du fournisseur identité**.
     * Copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **URL de déconnexion du fournisseur identité**.
  7. Dans **SAML User ID Type**, sélectionnez **Assertion contains User’s sprinklr.com username**.
  8. Dans **SAML User ID Location**, sélectionnez **User ID is in the Name Identifier element of the Subject statement**.
  9. Cliquez sur **Save**.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurer l’authentification unique")

## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur
Pour que les utilisateurs AAD puissent se connecter, leur accès doit être approvisionné dans l’application Sprinklr.  
Cette section décrit comment créer des comptes d’utilisateur AAD dans Sprinklr.

### <a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur dans SpringCM, procédez comme suit :
1. Connectez-vous à votre site d’entreprise Sprinklr en tant qu’administrateur.

2. Accédez à **Administration \> Settings**.
   
    ![Administration](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3. Accédez à **Manage Client \> Users** dans le volet gauche.
   
    ![Paramètres](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Paramètres")

4. Cliquez sur **Add User**.
   
    ![Paramètres](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Paramètres")

5. Dans la page **Edit User** , procédez comme suit :
   
    ![Modifier l’utilisateur](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Modifier l’utilisateur") 
  1. Dans les zones de texte **Email**, **First Name** et **Last Name**, saisissez les informations du compte utilisateur Azure AD que vous souhaitez approvisionner.
  2. Sélectionnez **Password Disabled**.
  3. Sélectionnez une langue dans **Language**.
  4. Sélectionnez un type d’utilisateur dans **User Type**.
  5. Cliquez sur **Update**.
   
     >[!IMPORTANT]
     >**Password Disabled** pour permettre à un utilisateur de se connecter par le biais d’un fournisseur d’identité. 
     > 

6. Accédez à **Role**, puis procédez comme suit :
   
    ![Rôles de partenaires](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Rôles de partenaires")
 1. Dans la liste **Global**, sélectionnez **ALL\_Permissions**.  
 2. Cliquez sur **Mettre à jour**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Sprinklr, pour approvisionner des comptes utilisateur AAD. 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Sprinklr, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.

2. Dans la page d’intégration d’application **Sprinklr**, cliquez sur **Affecter des utilisateurs**.
   
    ![Affecter des utilisateurs](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Affecter des utilisateurs")

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
    ![Oui](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


