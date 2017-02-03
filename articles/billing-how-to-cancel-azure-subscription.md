---
title: Annulation de votre abonnement Azure | Microsoft Docs
description: "Décrit comment annuler votre abonnement Azure, par exemple l’abonnement d’essai gratuit"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: c23c92234b38eeb911bd316fffe5127bbad4de91
ms.openlocfilehash: e64e4a3baf48ecd5b16bf5a141455152b7a486f5


---
# <a name="cancel-your-azure-subscription"></a>Annulation de votre abonnement Azure
Vous pouvez annuler votre abonnement Azure en tant qu’administrateur de compte. Après l’annulation de l’abonnement, vous n’avez plus accès aux ressources ni aux services Azure.

Avant d’annuler votre abonnement :

* Sauvegardez vos données. Par exemple, si vous stockez des données dans le stockage Azure ou SQL, téléchargez une copie. Si vous avez une machine virtuelle, enregistrez une image localement.
* Arrêtez les services. Accédez à la [page de ressources du portail de gestion](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) et **arrêtez** les machines virtuelles, les applications ou autres services en cours d’exécution.
* Envisagez de migrer vos données. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](./azure-resource-manager/resource-group-move-resources.md).

Si vous annulez un [plan de support Azure](https://azure.microsoft.com/support/plans/), vous restez facturé chaque mois jusqu’à la fin de la période de 6 mois.

## <a name="cancel-subscription-via-the-azure-portal"></a>Annuler l’abonnement par le biais du Portail Azure
1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.

   > [!NOTE]
   > Seul l’administrateur de compte peut annuler des abonnements. Pour plus d’informations sur la manière de déterminer qui est l’administrateur de compte de l’abonnement, voir le [Forum aux questions](billing-subscription-transfer.md#faq).
   >
   >

2. Dans le menu Hub, sélectionnez **Abonnements**.

    ![Bouton d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. Dans le panneau des abonnements, sélectionnez celui que vous souhaitez annuler et cliquez sur **Annuler l’abonnement** pour lancer le panneau d’annulation.

    ![cancel_ibiza](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. Entrez le nom de l’abonnement et choisissez un motif d’annulation, puis cliquez sur le bouton **Annuler l’abonnement** en bas du panneau Annuler.

> [!NOTE]
> L’annulation prend jusqu’à 10 minutes pour être reflétée dans le portail, mais la facturation est immédiatement arrêtée.
>
>

## <a name="cancel-subscription-via-the-azure-account-center"></a>Annuler l’abonnement par le biais du Centre des comptes Azure
1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.
2. Sous **Cliquez sur un abonnement pour consulter les détails et l’utilisation**, sélectionnez l’abonnement que vous souhaitez annuler.

    ![selectsub](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. Dans la partie droite de la page, cliquez sur **Annuler l’abonnement**.

    ![cancelsub](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. Sélectionnez **Oui, annulez mon abonnement**.

    ![cancelbox](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. Cliquez sur  ![checkbutton](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) pour fermer la boîte de dialogue et revenir à votre page d’abonnement.

Une fois votre abonnement annulé, nous attendons 90 jours avant de supprimer définitivement vos données au cas où vous deviez y accéder ou changiez d’avis. Pour plus d’informations, consultez [Centre de gestion de la confidentialité de Microsoft - Comment nous gérons vos données](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Réactivation de l’abonnement
Si vous avez annulé votre abonnement de paiement à l’utilisation par inadvertance, vous pouvez [le réactiver dans le Centre des comptes](billing-subscription-become-disable.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.



<!--HONumber=Dec16_HO2-->


