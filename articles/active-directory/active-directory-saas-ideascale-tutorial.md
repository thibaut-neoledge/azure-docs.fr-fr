---
title: "Didacticiel : Intégration d’Azure Active Directory avec IdeaScale | Microsoft Docs"
description: "Apprenez à utiliser IdeaScale avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2695483b7dad4418bf24f21ebe808dd145fb9503
ms.openlocfilehash: 6362e5b6232a3a42fdbad3cf0e5a36a2e4b178b8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Didacticiel : Intégration d’Azure Active Directory à IdeaScale
L’objectif de ce didacticiel est de montrer comment intégrer Azure et IdeaScale.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement IdeaScale pour lequel l’authentification unique (SSO) est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à IdeaScale pourront s’authentifier de manière unique dans l’application à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

*  Activation de l’intégration d’application pour IdeaScale
*  Configuration de l'authentification unique
*  Configuration de l'approvisionnement des utilisateurs
*  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scénario")

## <a name="enable-the-application-integration-for-ideascale"></a>Activer l’intégration d’applications pour IdeaScale
Cette section décrit l’activation de l’intégration d’application pour IdeaScale.

**Pour activer l’intégration d’applications pour IdeaScale, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **IdeaScale**.
   
   ![Galerie d’applications](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **IdeaScale**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur IdeaScale avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  

La configuration de l’authentification unique pour IdeaScale vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **IdeaScale** du Portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à IdeaScale**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurer l’authentification unique")
3. Dans la zone de texte **URL de connexion de IdeaScale** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application IdeaScale (par exemple : « *https://company.IdeaScale.com* »), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur IdeaScale**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise IdeaScale en tant qu’administrateur.
6. Accédez à **Community Settings**.
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
7. Accédez à **Security \> Single Signon Settings**.
   
   ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")
8. Pour **Single-Signon Type**, sélectionnez **SAML 2.0**.
   
   ![Single Signon Type](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. Dans la page de boîte de dialogue **Paramètres de l’authentification unique** , procédez comme suit :
   
   ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")
   
   1. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur IdeaScale**, copiez la valeur **ID d’identité**, puis collez-la dans la zone de texte **SAML IdP Entity ID**.
   2. Copiez le contenu du fichier de métadonnées téléchargé et collez-le dans la zone de texte **SAML ldP Metadata** .
   3. Dans la page **Configurer l’authentification unique sur IdeaScale** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout Success URL**.
   4. Cliquez sur **Enregistrer les modifications**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurer l’authentification unique")
    
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour permettre aux utilisateurs Azure AD de se connecter à IdeaScale, vous devez les approvisionner dans IdeaScale. Dans le cas d’IdeaScale, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **IdeaScale** en tant qu’administrateur.
2. Accédez à **Community Settings**.
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
3. Accédez à **Basic Settings \> Member Management**.
4. Cliquez sur **Add Member**.
   
   ![Member Management](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. Dans la section Add New Member, procédez comme suit :
   
   ![Add New Member](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. Indiquez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans la zone de texte **Email Addresses** .
   2. Cliquez sur **Save Changes**. 
   
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory reçoit un message électronique avec un lien pour confirmer le compte avant qu’il ne soit activé.
    >  

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par IdeaScale pour approvisionner des comptes d’utilisateur Azure Active Directory.
>  

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à IdeaScale, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **IdeaScale**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


