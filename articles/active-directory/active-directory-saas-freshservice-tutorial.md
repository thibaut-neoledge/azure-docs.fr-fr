---
title: "Didacticiel : Intégration d’Azure Active Directory avec FreshService | Microsoft Docs"
description: "Apprenez à utiliser FreshService avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 64421f1b7424751332b76508ae65d009a3e1f48b
ms.openlocfilehash: df90d455f2c3bdbc59c38447fe65bf1b64979249
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Didacticiel : Intégration d’Azure Active Directory à FreshService
L’objectif de ce didacticiel est de montrer comment intégrer Azure et FreshService.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement FreshService pour lequel l’authentification unique (SSO) est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à FreshService pourront s’authentifier de manière unique dans l’application à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

* Activation de l’intégration d’application pour FreshService
* Configuration de l'authentification unique
* Configuration de l'approvisionnement des utilisateurs
* Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scénario")

## <a name="enable-the-application-integration-for-freshservice"></a>Activer l’intégration d’applications pour FreshService
Cette section décrit l’activation de l’intégration d’application pour FreshService.

**Pour activer l’intégration d’applications pour FreshService, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **FreshService**.
   
   ![Galerie d’applications](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **FreshService**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur FreshService avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. 

La configuration de l’authentification unique pour FreshService oblige à récupérer une valeur d’empreinte numérique dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **FreshService** du Portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à FreshService**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurer l’authentification unique")
3. Dans la zone de texte **URL de connexion à FreshService** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application FreshService (par ex., **http://democompany.freshservice.com/**), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur FreshService**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise FreshService en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrateur")
7. Dans le **Customer Portal**, cliquez sur **Security**.
   
   ![Sécurité](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sécurité")
8. Dans la section **Security** , procédez comme suit :
   
   ![Single Sign On](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")
   
   1. Activez **Single Sign On**.
   2. Sélectionnez **SAML SSO**.
   3. Dans la page **Configurer l’authentification unique sur FreshService** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **SAML Login URL**.
   4. Dans la page **Configurer l’authentification unique sur FreshService** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout URL**.
   5. Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Security Certificate Fingerprint**.
   
      >[!TIP]
      >Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
      >
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour permettre aux utilisateurs Azure AD de se connecter à FreshService, vous devez les approvisionner dans FreshService. Dans le cas de FreshService, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise **FreshService** en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrateur")
3. Dans la section **User Management**, cliquez sur **Requesters**.
   
   ![Requesters](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")
4. Cliquez sur **New Requester**.
   
   ![New Requesters](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. Dans la section **New Requester** , procédez comme suit :
   
   ![New Requester](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")   
   1. Entrez le prénom et l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte **Prénom** et **Email**.
   2. Cliquez sur **Save**.
   
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.
    >  

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par FreshService, pour approvisionner des comptes utilisateur AAD.
>  

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à FreshService, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **FreshService**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


