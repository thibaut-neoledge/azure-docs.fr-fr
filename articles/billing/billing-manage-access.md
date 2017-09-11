---
title: "Gérer l’accès à la facturation Azure à l’aide de rôles | Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.contentlocale: fr-fr
ms.lasthandoff: 08/23/2017

---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Gérer l’accès aux informations de facturation pour Azure à l’aide du contrôle d’accès en fonction du rôle

Vous pouvez accorder l’accès aux informations de facturation Azure aux membres de votre équipe en attribuant un des rôles d’utilisateur suivants à votre abonnement : Administrateur de compte, Administrateur de service, Coadministrateur, Propriétaire, Collaborateur, Lecteur et Lecteur de facturation. Ils accèdent ainsi aux informations de facturation dans le [portail Azure](https://portal.azure.com/) et peuvent utiliser les [API de facturation](billing-usage-rate-card-overview.md) pour obtenir par programmation des factures (une fois l’adhésion effectuée) et des détails sur l’utilisation. Pour plus d’informations sur les utilisateurs pouvant accorder des rôles, et sur ce que les rôles permettent de faire, consultez [Rôles dans Azure RBAC](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a> Autoriser d’autres utilisateurs à accéder aux factures

L’administrateur de compte doit adhérer via le [portail Azure](https://portal.azure.com/) pour autoriser l’accès aux factures à d’autres utilisateurs et via l’API.

1. En tant qu’administrateur de compte, sélectionnez votre abonnement dans le [panneau Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

1. Sélectionnez **Factures**, puis **Accéder aux factures**.

    ![Capture d’écran montrant comment déléguer l’accès aux factures](./media/billing-manage-access/AA-optin.png)

1. **Activez** l’accès, puis enregistrez les modifications pour permettre aux utilisateurs associés aux rôles étendus d’abonnement de télécharger la facture.

    ![Capture d’écran montrant les options Activé-Désactivé pour déléguer l’accès aux factures](./media/billing-manage-access/AA-optinAllow.png)

Le fait d’adhérer permet à l’Administrateur de service, Coadministrateur, Propriétaire, Collaborateur, Lecteur et Lecteur de facturation de l’abonnement de télécharger les factures au format PDF dans le portail Azure. Les factures antérieures à décembre 2016 ne sont toutefois accessibles qu’à l’administrateur de compte pour le moment.

L’administrateur de compte peut également être configuré afin de recevoir les factures par courrier électronique. Pour en savoir plus, consultez [Obtenir votre facture par courrier électronique](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Ajouter des utilisateurs au rôle Lecteur de facturation

Le rôle Lecteur de facturation dispose d’un accès en lecture seule aux informations de facturation d’abonnement dans le portail Azure, mais ne peut pas accéder à des services tels que les machines virtuelles et les comptes de stockage. Attribuez le rôle Lecteur de facturation à un utilisateur devant accéder aux informations de facturation d’abonnement, mais sans la possibilité de gérer des services Azure. Ce rôle convient aux utilisateurs d’une organisation qui procèdent uniquement à la gestion financière et des coûts pour des abonnements Azure.

1. Sélectionnez votre abonnement dans le [panneau Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

1. Sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **Ajouter**.

    ![Capture d’écran montrant IAM dans le panneau de l’abonnement](./media/billing-manage-access/select-iam.PNG)

1. Choisissez **Lecteur de facturation** dans la page **Sélectionner un rôle**.

    ![Capture d’écran montrant le lecteur de facturation dans la vue contextuelle](./media/billing-manage-access/select-roles.PNG)

1. Saisissez le courrier électronique pour l’utilisateur que vous souhaitez inviter, puis cliquez sur **OK** pour envoyer l’invitation.

    ![Capture d’écran montrant la saisie d’un courrier électronique pour inviter un utilisateur](./media/billing-manage-access/add-user.PNG)

1. Suivez les instructions fournies dans le courrier électronique d’invitation pour vous connecter en tant que lecteur de facturation.

    ![Capture d’écran montrant ce que le lecteur de facturation peut voir dans le portail Azure](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> La fonctionnalité Lecteur de facturation est en préversion et ne prend pas encore en charge les abonnements d’entreprise ni les clouds non globaux.

## <a name="adding-users-to-other-roles"></a>Ajouter des utilisateurs à d’autres rôles

Les utilisateurs d’autres rôles, tels que Propriétaire ou Collaborateur, peuvent non seulement accéder aux informations de facturation, mais également à des services Azure. Pour gérer ces rôles, consultez [Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Qui peut accéder au [Centre des comptes](https://account.windowsazure.com) ?

Seul l’administrateur de compte peut se connecter au centre des comptes. L’administrateur de compte est le propriétaire légal de l’abonnement. Par défaut, l’utilisateur qui s’est inscrit ou a acheté l’abonnement Azure est l’administrateur de compte, sauf si la [propriété de l’abonnement a été transférée](billing-subscription-transfer.md) à quelqu’un d’autre. L’administrateur de compte peut créer des abonnements, annuler des abonnements, modifier l’adresse de facturation d’un abonnement et gérer les stratégies d’accès de l’abonnement.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

