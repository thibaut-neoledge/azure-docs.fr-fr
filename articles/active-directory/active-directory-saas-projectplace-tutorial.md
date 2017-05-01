---
title: "Didacticiel : intégration d’Azure Active Directory à Projectplace | Microsoft Docs"
description: "Apprenez à utiliser Projectplace avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fcf3332a6674524ea5354b96826586c79a4ba3e1
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Didacticiel : Intégration d’Azure Active Directory à Projectplace
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Projectplace. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Projectplace pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Projectplace pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Projectplace (connexion initiée par le fournisseur du service) en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Projectplace
2. Configuration de l’authentification unique (SSO)
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scénario")

## <a name="enabling-the-application-integration-for-projectplace"></a>Activation de l’intégration d’application pour Projectplace
Cette section décrit l’activation de l’intégration d’application pour Projectplace.

**Pour activer l’intégration d’application pour Projectplace, procédez comme suit :**
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Projectplace**.
   
   ![Galerie d’applications](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **Projectplace**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Projectplace avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Projectplace**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Projectplace**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Projectplace**, tapez l’URL de votre locataire Projectplace (par exemple, *http://entreprise.projectplace.com*), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Projectplace**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configurer l’authentification unique")
5. Envoyez le fichier de métadonnées à l’équipe du support technique Projectplace.
   
   >[!NOTE]
   >La configuration de l’authentification unique doit être effectuée par l’équipe du support technique Projectplace. Vous recevez une notification dès qu’elle est terminée.
   > 
   > 
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour permettre aux utilisateurs Azure AD de se connecter à Projectplace, vous devez les approvisionner dans Projectplace. Dans le cas de Projectplace, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise **Projectplace** en tant qu’administrateur.
2. Accédez à **People**, puis cliquez sur **Members**.
   
   ![Personnes](./media/active-directory-saas-projectplace-tutorial/IC790228.png "Personnes")
3. Cliquez sur **Add Member**.
   
   ![Ajouter des membres](./media/active-directory-saas-projectplace-tutorial/IC790232.png "ajouter des membres")
4. Dans la section **Add Member** , procédez comme suit :
   
   ![Nouveaux membres](./media/active-directory-saas-projectplace-tutorial/IC790233.png "nouveaux membres")
   
   1. Dans la zone de texte **New Members** , entrez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
   2. Cliquez sur **Envoyer**.

Un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé est envoyé au titulaire du compte Azure Active Directory.


>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Projectplace pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Projectplace, procédez comme suit :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Projectplace**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


