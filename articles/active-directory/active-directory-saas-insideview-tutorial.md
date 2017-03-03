---
title: "Didacticiel : Intégration d’Azure Active Directory avec InsideView | Microsoft Docs"
description: "Apprenez à utiliser InsideView avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 48a8cb0afd9fc2f9201169b074cf10f6d910952f
ms.openlocfilehash: 235b1f11c60a595dfc760701213cfc1b5f792b61
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Didacticiel : Intégration d’Azure Active Directory à InsideView
L’objectif de ce didacticiel est de montrer comment intégrer Azure et InsideView.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire InsideView

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à InsideView pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise InsideView (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

* Activation de l’intégration d’application pour InsideView
* Configuration de l'authentification unique
* Configuration de l'approvisionnement des utilisateurs
* Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scénario")

## <a name="enable-the-application-integration-for-insideview"></a>Activer l’intégration d’applications pour InsideView
Cette section décrit l’activation de l’intégration d’application pour InsideView.

**Pour activer l’intégration d’applications pour InsideView, suivez les étapes ci-dessous :**
 
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-insideview-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-insideview-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **InsideView**.
   
   ![Galerie d’applications](./media/active-directory-saas-insideview-tutorial/IC794129.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **InsideView**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur InsideView avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
 
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **InsideView** du Portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à InsideView**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de réponse InsideView**, tapez votre URL d’authentification InsideView (par exemple, `https://my.insideview.com/iv/<STS Name>/login.iv`), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur InsideView**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise InsideView en tant qu’administrateur.
6. Dans la barre d’outils située en haut, cliquez sur **Admin**, **SingleSignOn Settings**, puis sur **Add SAML**.
   
   ![SAML Single Sign On Settings](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. Dans la section **Add a New SAML** , procédez comme suit :
   
   ![Add a New SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. Dans la zone de texte **STS Name** , attribuez un nom à votre configuration.
   2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur InsideView**, copiez la valeur **Point de terminaison initié du fournisseur du services**, puis collez-la dans la zone de texte **SamlP/WS-Fed Unsolicated EndPoint**.
   3. Créez un fichier **codé en base 64** à partir du certificat téléchargé.      

     >[!TIP]
     >Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o). 
     >
   4. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **STS Certificate** .
   5. Renseignez les informations suivantes :
    * Dans la zone de texte **Crm User Id Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    * Dans la zone de texte **Crm Email Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    * Dans la zone de texte **Crm First Name Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    * Dans la zone de texte **Crm lastName Mapping**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Cliquez sur **Save**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurer l’authentification unique")
   
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour permettre aux utilisateurs Azure AD de se connecter à InsideView, vous devez les approvisionner dans InsideView. Dans le cas d’InsideView, l’approvisionnement est une tâche manuelle.

Pour obtenir des utilisateurs ou des contacts créés dans InsideView, contactez votre responsable de réussite client ou envoyez un courrier électronique à **support@insideview.com**

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par InsideView, pour approvisionner des comptes utilisateur AAD.
>  

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à InsideView, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **InsideView**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-insideview-tutorial/IC794138.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-insideview-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


