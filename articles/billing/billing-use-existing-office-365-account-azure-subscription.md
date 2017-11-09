---
title: "Inscrivez-vous à Azure avec un compte Office 365 | Microsoft Docs"
description: "Apprenez à créer un abonnement à Azure avec un compte Office 365."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: cjiang
ms.openlocfilehash: 923b3ca8a2e2b1d7f44cd77946a02e1d30839f14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Souscrire un abonnement Azure avec un compte Office 365
Si vous avez un abonnement à Office 365, vous pouvez utiliser votre compte Office 365 pour créer un abonnement Azure. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre nom d’utilisateur et votre mot de passe Office 365. Si vous souhaitez configurer des machines virtuelles ou utiliser d’autres services Azure, vous devez souscrire un abonnement Azure. Vous pouvez partager votre abonnement Azure avec d’autres personnes et [utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès à vos ressources et abonnement Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Si vous avez déjà à la fois un compte Office 365 et un abonnement Azure, consultez la page [Associer un client Office 365 à un abonnement Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obtenir un abonnement Azure avec un compte Office 365

Gagnez du temps et évitez la prolifération de comptes en vous inscrivant sur Azure à l’aide de vos nom d’utilisateur et mot de passe Office 365. 

1. Inscrivez-vous sur [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Connectez-vous avec votre nom d’utilisateur et votre mot de passe Office 365. Le compte que vous utilisez n’a pas besoin de disposer d’autorisations d’administrateur. Si vous avez plusieurs comptes Office 365, veillez à utiliser les informations d’identification du compte Office 365 que vous souhaitez associer à votre abonnement Azure. 

   ![Capture d’écran montrant la page de connexion.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Entrez les informations requises et terminez le processus d’inscription. Certaines informations ne sont peut-être pas nécessaires si vous avez déjà un compte Office 365.

    ![Capture d’écran montrant le formulaire d’inscription.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Si vous devez ajouter d’autres personnes de votre organisation à l’abonnement Azure, consultez la page [Bien démarrer avec la gestion des accès sur le Portail Azure](../active-directory/role-based-access-control-what-is.md). 

## <a id="more-about-subs">En savoir plus sur les abonnements Azure et Office 365</a>
Office 365 et Azure utilisent le service Azure AD pour gérer les utilisateurs et les abonnements. Le répertoire Azure est comme un conteneur qui vous sert à regrouper les abonnements et les utilisateurs. Pour utiliser les mêmes comptes d’utilisateur pour vos abonnements Azure et Office 365, vous devez vous assurer que les abonnements Azure sont créés dans le même répertoire que les abonnements Office 365. Gardez à l’esprit les points suivants :

* Un abonnement est créé dans un répertoire.
* Les utilisateurs appartiennent à des répertoires.
* Un abonnement est créé dans le répertoire de l’utilisateur qui crée l’abonnement. Par conséquent, votre abonnement à Office 365 est lié au même compte que votre abonnement Azure.
* Les abonnements Azure sont détenus par des utilisateurs individuels dans le répertoire.
* Les abonnements Office 365 sont détenus par le répertoire proprement dit. Les utilisateurs possédant les autorisations correspondantes dans le répertoire peuvent gérer ces abonnements.

![Capture d’écran montrant la relation entre le répertoire, les utilisateurs et les abonnements.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Pour plus d’informations, consultez la page [Association des abonnements Azure avec Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème. 
