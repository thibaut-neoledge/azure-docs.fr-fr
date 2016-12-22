---
title: "Didacticiel : Intégration d’Azure Active Directory à Workplace par Facebook | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workplace by Facebook."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Didacticiel : Intégration d’Azure Active Directory à Workplace by Facebook
L’objectif de ce didacticiel est de vous montrer comment intégrer Workplace by Facebook à Azure Active Directory (Azure AD).

L’intégration de Workplace by Facebook à Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à Workplace by Facebook. 
* Vous pouvez configurer automatiquement les comptes des utilisateurs auxquels l’accès à Workplace by Facebook a été accordé.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Workplace by Facebook (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central. 

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables
Pour configurer l’intégration d’Azure AD avec CS Stars, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Workplace by Facebook avec l’authentification unique activée

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Ajout de Workplace by Facebook à partir de la galerie
Pour configurer l’intégration de Workplace by Facebook à Azure AD, vous devez ajouter Workplace by Facebook à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workplace by Facebook à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Workplace by Facebook**.
7. Dans le volet de résultats, sélectionnez **Workplace by Facebook**, puis cliquez sur **Terminer** pour ajouter l’application.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Workplace by Facebook avec leur compte Azure Active Directory, en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique Azure AD avec Workplace by Facebook, procédez comme suit :**

1. Après avoir ajouté Workplace by Facebook dans le portail Azure Classic, cliquez sur **Configurer l’authentification unique**.
2. Dans l’écran **Configurer l’URL de l’application**, entrez l’URL à l’emplacement où les utilisateurs se connecteront à votre application Workplace by Facebook. Voici l’URL de votre locataire Workplace by Facebook (exemple : https://example.facebook.com/). Une fois que vous avez terminé, cliquez sur **Suivant**.
3. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Workplace by Facebook en tant qu’administrateur.
4. Suivez les instructions à l’adresse suivante pour configurer Workplace by Facebook pour utiliser Azure AD comme fournisseur d’identité : [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. Une fois que vous avez terminé, revenez aux fenêtres de navigateur affichant le portail Azure Classic, cochez la case pour confirmer que vous avez terminé la procédure, puis cliquez sur **Suivant** et **Terminé**.


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>Approvisionnement automatique des utilisateurs à Workplace by Facebook
Azure AD prend en charge la possibilité de synchroniser automatiquement les informations de compte des utilisateurs affectés à Workplace by Facebook. La synchronisation automatique permet à Workplace by Facebook d’obtenir les données nécessaires pour autoriser l’accès des utilisateurs, avant leur première tentative de connexion. Elle annule également l’approvisionnement des utilisateurs à Workplace by Facebook lorsque l’accès a été révoqué dans Azure AD.

Vous pouvez configurer l’approvisionnement automatique en cliquant sur **Configurer l’approvisionnement de comptes** dans la fenêtre du portail Azure Classic.

Pour plus d’informations sur la façon de configurer l’approvisionnement automatique, consultez [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>Affectation d’utilisateurs à Workplace by Facebook
Pour que les utilisateurs AAD approvisionnés puissent voir Workplace by Facebook sur leur volet d’accès, l’accès doit leur avoir été affecté à l’intérieur du portail Azure Classic.

**Affectation d’utilisateurs à Workplace by Facebook :**

1. Sur la page de démarrage de Workplace by Facebook dans le portail Azure Classic, cliquez sur **Affecter des comptes**.
2. Dans le menu **Afficher** , sélectionnez si vous souhaitez affecter un utilisateur ou un groupe à Workplace by Facebook et cliquez sur la coche.
3. Dans la liste obtenue, sélectionnez les utilisateurs ou le groupe auxquels vous souhaitez affecter Workplace by Facebook.
4. Dans le pied de page, cliquez sur le bouton **Attribuer** .

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


