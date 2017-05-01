---
title: "Inscrivez-vous à Azure avec un compte Office 365 | Microsoft Docs"
description: "Apprenez à créer un abonnement à Azure avec un compte Office 365."
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d62ec023605573e4b0804da00c75f65c8dea5173
ms.lasthandoff: 04/12/2017


---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Souscrire un abonnement Azure avec un compte Office 365
Si vous avez un abonnement à Office 365, utilisez votre compte Office 365 pour créer un abonnement Azure. Vous pouvez vous connecter au [Portail Azure](https://portal.azure.com/) sans abonnement Azure avec votre nom d’utilisateur et votre mot de passe Office 365. Mais, si vous souhaitez configurer des machines virtuelles ou utiliser d’autres services Azure, vous devez créer un abonnement Azure pour votre compte. Si vous faites partie d’une organisation qui souhaite utiliser les services Azure, ajoutez d’autres personnes de votre organisation à l’abonnement après sa création.  

Si vous avez déjà à la fois un compte Office 365 et un abonnement Azure, consultez la page [Associer un client Office 365 à un abonnement Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-by-using-your-office-365-account"></a>Obtenir un abonnement Azure avec un compte Office 365

Pour vous inscrire à Azure, utilisez le lien Azure gratuit et inscrivez-vous à Azure avec votre nom d’utilisateur et votre mot de passe Office 365. 

1. Accédez à [Azure.com](https://azure.microsoft.com/). 
2. Cliquez sur **Commencer gratuitement**.
3. Connectez-vous avec votre nom d’utilisateur et votre mot de passe Office 365. Le compte que vous utilisez n’a pas besoin de disposer d’autorisations d’administrateur. Si vous avez plusieurs comptes Office 365, veillez à utiliser les informations d’identification du compte Office 365 que vous souhaitez associer à votre abonnement Azure. 

   ![Capture d’écran montrant la page de connexion.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

4. Entrez les informations requises et terminez le processus d’inscription.

    ![Capture d’écran montrant le formulaire d’inscription.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Si vous devez ajouter d’autres personnes de votre organisation à l’abonnement Azure, consultez la page [Bien démarrer avec la gestion des accès sur le Portail Azure](../active-directory/role-based-access-control-what-is.md). 
- Si vous souhaitez ajouter un administrateur pour qu’il participe à la gestion de l’abonnement, consultez la page [Ajouter ou modifier les rôles d’administrateur Azure qui gèrent l’abonnement ou le service](billing-add-change-azure-subscription-administrator.md).

## <a id="more-about-subs">En savoir plus sur les abonnements Azure et Office 365</a>
Office 365 et Azure utilisent le service Azure AD pour gérer les utilisateurs et les abonnements. Le répertoire Azure est comme un conteneur qui vous sert à regrouper les abonnements et les utilisateurs. Pour utiliser les mêmes comptes d’utilisateur pour vos abonnements Azure et Office 365, vous devez vous assurer que les abonnements sont créés dans le même répertoire. Gardez à l’esprit les points suivants :

* Un abonnement est créé dans un répertoire.
* Les utilisateurs appartiennent à des répertoires.
* Un abonnement est créé dans le répertoire de l’utilisateur qui crée l’abonnement. Par conséquent, votre abonnement à Office 365 est lié au même compte que votre abonnement Azure.
* Les abonnements Azure sont détenus par des utilisateurs individuels dans le répertoire.
* Les abonnements Office 365 sont détenus par le répertoire proprement dit. Les utilisateurs possédant les autorisations correspondantes dans le répertoire peuvent gérer ces abonnements.

![Capture d’écran montrant la relation entre le répertoire, les utilisateurs et les abonnements.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Pour plus d’informations, consultez la page [Association des abonnements Azure avec Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème. 
