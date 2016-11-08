---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Lynda.com | Microsoft Docs'
description: Apprenez à utiliser Lynda.com avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/08/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory à Lynda.com
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Lynda.com. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Lynda.com

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Lynda.com pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Lynda.com (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’applications pour Lynda.com
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scénario")

## Activation de l’intégration d’applications pour Lynda.com
Cette section décrit l'activation de l'intégration de l'application pour Lynda.com.

### Pour activer l'intégration d’applications pour Lynda.com, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-lynda-tutorial/IC749321.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-lynda-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **Lynda.com**.
   
   ![Galerie d’applications](./media/active-directory-saas-lynda-tutorial/IC777524.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **Lynda.com**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
   
   ## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s'authentifier sur Lynda.com avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

> [!IMPORTANT]
> Pour être en mesure de configurer l'authentification unique sur votre locataire Lynda.com, vous devez au préalable contacter le support technique Lynda.com pour faire activer cette fonctionnalité.
> 
> 

### Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic puis dans la page d’intégration d’applications **Lynda.com**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Lynda.com**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Lynda.com**, tapez l’URL de votre locataire Lynda.com (par exemple, *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*) puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Lynda.com**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurer l’authentification unique")
5. Envoyez le fichier de métadonnées téléchargé à l’équipe de support technique Lynda.com. L'équipe de support technique Lynda.com effectue la configuration de l'authentification unique pour vous.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurer l’authentification unique")
   
   ## Configuration de l'approvisionnement des utilisateurs

Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lynda.com. Lorsqu'un utilisateur tente de se connecter à Lynda.com à l'aide du panneau d'accès, Lynda.com vérifie si cet utilisateur existe. Si aucun compte d'utilisateur n’est disponible, Lynda.com le crée automatiquement.

> [!NOTE]
> Vous pouvez utiliser n'importe quel outil ou API de création de compte utilisateur, fourni par Lynda.com, pour approvisionner des comptes utilisateur AAD.
> 
> 

## Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à Lynda.com, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Lynda.com**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-lynda-tutorial/IC777531.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-lynda-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->