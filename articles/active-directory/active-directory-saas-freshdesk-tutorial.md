---
title: "Didacticiel : Intégration d’Azure Active Directory avec Freshdesk | Microsoft Docs"
description: "Apprenez à utiliser Freshdesk avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31177994-7910-4a72-bd76-5fa6260242fb
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7043787c74c138984fdf5370784678d6dfcaa81e


---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Intégration d’Azure Active Directory à Freshdesk
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Freshdesk.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Freshdesk

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Freshdesk pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Freshdesk (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Freshdesk
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario")

## <a name="enabling-the-application-integration-for-freshdesk"></a>Activation de l’intégration d’application pour Freshdesk
Cette section décrit l’activation de l’intégration d’application pour Freshdesk.

### <a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Pour activer l'intégration d’applications pour Freshdesk, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, tapez **Freshdesk**.
   
   ![Galerie d’applications](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Application gallery")
7. Dans le volet des résultats, sélectionnez **Freshdesk**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Freshdesk avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l'authentification unique pour Freshdesk oblige à récupérer une valeur d’empreinte numérique dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic puis dans la page d’intégration d’applications **Freshdesk**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Freshdesk**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Freshdesk**, tapez votre URL selon le modèle suivant « *https://\<nom_locataire\>.Freshdesk.com* », puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur Freshdesk**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous **c:\\Freshdesk.cer**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshdesk en tant qu’administrateur.
6. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")
7. Dans l’onglet **General Settings**, cliquez sur **Security**.
   
   ![Sécurité](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")
8. Dans la section **Security** , procédez comme suit :
   
   ![Authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")
   
   1. Pour **Single Sign On (SSO)**, sélectionnez **On**.
   2. Sélectionnez **SAML SSO**.
   3. Dans la page **Configurer l’authentification unique sur Freshdesk** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **SAML Login URL**.
   4. Dans la page **Configurer l’authentification unique sur Freshdesk** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout URL**.
   5. Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Security Certificate Fingerprint**.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Cliquez sur **Save**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Freshdesk, vous devez les approvisionner dans Freshdesk.  
Dans le cas de Freshdesk, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre locataire **Freshdesk** .
2. Dans le menu situé en haut, cliquez sur **Admin**.
   
   ![Administrateur](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")
3. Dans l’onglet **General Settings**, cliquez sur **Agents**.
   
   ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")
4. Cliquez sur **New Agent**.
   
   ![New Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")
5. Dans la boîte de dialogue Agent Information, procédez comme suit :
   
   ![Informations sur l’agent](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   1. Dans la zone de texte **Full Name** , tapez le nom du compte Azure AD que vous souhaitez approvisionner.
   2. Dans la zone de texte **Email** , tapez l’adresse de messagerie Azure AD du compte Azure AD que vous souhaitez approvisionner.
   3. Dans la zone de texte **Title** , tapez le titre du compte Azure AD que vous souhaitez approvisionner.
   4. Sélectionnez **Agents role**, puis cliquez sur **Assign**.
   5. Cliquez sur **Save**.
      
      > [!NOTE]
      > Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé.
      > 
      > 

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Freshdesk, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Freshdesk, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Freshdesk**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


