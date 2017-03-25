---
title: "Azure Active Directory B2C : création d’un client Azure Active Directory B2C | Microsoft Docs"
description: "Rubrique sur la création d’un client Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 02bb7286f6cd42f86e6cc920742a86d57ed93b60
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C : création d’un client Azure AD B2C
Pour commencer à utiliser Microsoft Azure Active Directory (Azure AD) B2C, suivez les quatre étapes décrites dans cet article.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Étape 1 : souscrire un abonnement Azure
Vous pouvez ignorer cette étape si vous possédez déjà un abonnement Azure. Dans le cas contraire, souscrivez [un abonnement Azure](../active-directory/sign-up-organization.md) et accédez à Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Étape 2 : créer un client Azure AD B2C
Procédez comme suit pour créer un client Azure AD B2C. Actuellement, les fonctionnalités B2C ne peuvent pas être activées dans vos clients existants.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur d’abonnements. Il s’agit du compte professionnel ou scolaire, ou du compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
2. Cliquez sur **Nouveau** (ou sur le bouton + bouton si réduit) puis, dans le champ **Rechercher dans le marketplace**, saisissez « Azure Active Directory B2C ».
   
    ![Capture d’écran de recherche de l’option Azure AD B2C](./media/active-directory-b2c-get-started/find-azure-ad-b2c.png)
3. Dans le résultat, cliquez sur **Azure Active Directory B2C**

    ![Capture d’écran de résultat pour Azure Active Directory B2C](./media/active-directory-b2c-get-started/find-azure-ad-b2c-result.png)
4. Vous allez afficher une page avec les détails sur B2C.  Cliquez sur **Créer** en bas pour commencer la configuration de votre nouveau client Azure Active Directory B2C.
5. Cliquez sur **Créer un nouveau client Azure Active Directory B2C**.
6. Choisissez un **nom d’organisation, un nom de domaine et un pays ou une région** pour votre client.

    ![Capture d’écran du formulaire de création d’un nouveau client](./media/active-directory-b2c-get-started/create-new-b2c-tenant.png)
7. Cliquez sur **Créer** pour créer votre client.  Cette opération peut prendre quelques minutes, vous serez alerté dans vos notifications lorsqu’elle sera terminée.

8. Votre client est désormais créé et apparaît dans l’extension Active Directory. Vous êtes par ailleurs administrateur général du client. Vous pouvez ajouter d’autres administrateurs généraux en fonction des besoins.
   
   > [!IMPORTANT]
   > Si vous envisagez d’utiliser un client B2C pour une application de production, consultez l’article sur [les clients de mise à l’échelle pour production/clients B2C de la version préliminaire](active-directory-b2c-reference-tenant-type.md). Notez qu’il existe des problèmes connus liés à la suppression d’un client B2C existant et à sa recréation sous le même nom de domaine. Vous devez créer un client B2C portant un nom de domaine différent.
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Étape 3 : accéder au panneau de fonctionnalités B2C sur le portail Azure
1. Développez **Plus de services** sous la barre de navigation à gauche.
2. Recherchez **Azure Active Directory B2C**, puis cliquez sur le résultat (vous pouvez le marquer comme favori pour y accéder plus facilement à l’avenir).

    ![Capture d’écran de recherche d’Azure Active Directory B2C dans le volet de navigation](./media/active-directory-b2c-get-started/navigate-to-azure-ad-b2c.png)

3. Le portail Azure avec le panneau de fonctionnalités B2C s’ouvre dans un nouvel onglet ou une nouvelle fenêtre de navigateur.
   
4. Épinglez ce panneau sur votre Tableau d’accueil pour en faciliter l’accès. (L’outil Épingler se trouve dans le coin supérieur droit du panneau de fonctionnalités.)
   
    ![Capture d’écran du panneau de fonctionnalités B2C et du bouton Épingler](./media/active-directory-b2c-get-started/b2c-pin-tenant.png)

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>Étape 4 : Lier votre locataire Azure Active Directory B2C à votre abonnement Azure
Si vous prévoyez d’utiliser votre locataire B2C pour des applications de production, vous devez lier votre locataire Azure Active Directory B2C à votre abonnement Azure pour payer les frais d’utilisation. Pour découvrir comment procéder, lisez cet [article](active-directory-b2c-how-to-enable-billing.md).

   > [!IMPORTANT]
   > Si vous ne liez votre locataire Azure Active Directory B2C à votre abonnement Azure, vous voyez un message d’avertissement (« Aucun abonnement lié à ce locataire B2C ou vérifiez votre abonnement. ») dans le panneau des fonctionnalités B2C sur le portail Azure. Il est important d’effectuer cette étape avant de passer vos applications en production.
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Accès facile au panneau de fonctionnalités B2C sur le portail Azure
Pour améliorer la détectabilité, nous avons ajouté un raccourci dans le panneau de fonctionnalités B2C sur le portail Azure.

1. Connectez-vous au portail Azure en tant qu’administrateur général de votre client B2C. Si vous êtes déjà connecté à un autre client, basculez entre les clients (dans le coin supérieur droit).
2. Cliquez sur **Parcourir** dans le volet de navigation de gauche.
3. Cliquez sur **Azure AD B2C** pour accéder au panneau de fonctionnalités B2C.
   
    ![Capture d’écran du bouton Parcourir du panneau de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment inscrire une application avec Azure AD B2C et créer une application de démarrage rapide en lisant [Azure Active Directory B2C : inscription de votre application](active-directory-b2c-app-registration.md).


