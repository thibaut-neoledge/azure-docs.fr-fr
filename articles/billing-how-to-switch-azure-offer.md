---
title: Changer d’offre pour votre abonnement Azure | Microsoft Docs
description: Découvrez comment changer votre abonnement Azure et basculer vers une autre offre à l’aide du portail de gestion des abonnements
services: ''
documentationcenter: ''
author: genlin
manager: mbaldwin
editor: ''
tags: billing,top-support-issue

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: genli

---
# <a name="switch-your-azure-subscription-to-another-offer"></a>Changer d’offre pour votre abonnement Azure
En tant que client [de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/), vous pouvez être en mesure de basculer votre abonnement Azure vers une autre offre du [Centre des comptes](https://account.windowsazure.com/Subscriptions). Par exemple, vous pouvez utiliser cette fonctionnalité pour tirer parti des [crédits mensuels pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si vous sollicitez un [essai gratuit](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau vers une offre de paiement à l’utilisation](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).

#### <a name="what's-supported:"></a>Opérations prises en charge :
| À partir | À |
| --- | --- |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Pour les autres changements d’offre, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="switch-subscription-offer"></a>Changer d’offre d’abonnement
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions).
2. Sélectionnez votre abonnement de paiement à l’utilisation.
3. Cliquez sur **Changer d’offre**. Le bouton est disponible uniquement si vous avez souscrit à une offre de paiement à l’utilisation et que votre première période de facturation est achevée.
   
   ![Bouton de changement d’offre](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. **Sélectionnez l’offre souhaitée** dans la liste des offres vers lesquelles il est possible de basculer votre abonnement. La liste varie en fonction des adhésions auxquelles est associé votre compte. Si rien n’est disponible, examinez la [liste des offres disponibles vers lesquelles il est possible de basculer](#whats-supported) et vérifiez que vous disposez des adhésions appropriées. 
   
   ![Sélectionner une offre](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. Selon l’offre que vous envisagez d’adopter, une note peut apparaître sur l’impact du changement. Parcourez cette liste soigneusement et suivez les instructions avant de continuer.
   
   ![Points à noter](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. Vous pouvez renommer votre abonnement. Par défaut, nous le définissons sur le nom de la nouvelle offre. Cliquez sur **Changer d’offre** pour terminer le processus.
   
   ![Page de confirmation](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. C’est terminé ! Votre abonnement est désormais associé à la nouvelle offre.

## <a name="why-can't-i-switch-offers?"></a>Pourquoi ne puis-je pas changer les offres ?
Il est possible que le bouton **Changer pour une autre offre** ne s’affiche pas si :

* Vous n’avez pas souscrit à un modèle de [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Actuellement, seuls les abonnements de paiement à l’utilisation peuvent être basculés vers une autre offre.
  
  * Si vous sollicitez un [essai gratuit](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau vers une offre de paiement à l’utilisation](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).
  * Pour basculer vers une offre d’un abonnement différent, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Vous êtes toujours dans votre première période de facturation ; vous devez attendre l’expiration de votre première période de facturation avant de pouvoir changer d’offre.

Le message **Il n’y a pas d’offres disponibles dans votre région ou dans votre pays à l’heure actuelle.** s’affiche si :

* Vous n’êtes pas éligible pour changer d’offre. Consultez la [liste des offres vers lesquelles vous pouvez basculer](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing?"></a>Quel impact le basculement des offres Azure a-t-il sur mon service et ma facturation ?
Voilà ce qui se passe lorsque vous changez de plans Azure dans le Centre des comptes.

### <a name="access-to-services"></a>Accéder aux services
Aucun temps d’arrêt de service n’est à déplorer pour les utilisateurs associés à l’abonnement. Toutefois, l’offre pour laquelle vous changez peut avoir des restrictions. Par exemple, certaines offres interdisent l’utilisation pour la production. Dans ce cas, il vous faudrait déplacer les ressources de production vers un autre abonnement.

### <a name="billing"></a>Facturation
Le jour où vous changez d’offre, une facture est générée pour l’ensemble des frais dus. Par la suite, votre abonnement est facturé en fonction des conditions de tarification de la nouvelle offre. La date de facturation de votre abonnement devient la date à laquelle vous avez changé d’offre. Les données d’utilisation et de facturation antérieures au changement d’offre n’étant pas conservées, nous vous recommandons d’en télécharger une copie avant le changement.

> [!NOTE]
> En raison de contraintes liées à la facturation, vous ne pouvez pas changer d’offre pendant le premier cycle de facturation qui suit la création d’un abonnement.
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-[cloud-solution-provider](https://partner.microsoft.com/solutions/cloud-reseller-overview)-(csp)-or-[enterprise-agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)-(ea)?"></a>Puis-je migrer du modèle de paiement à l’utilisation vers un [fournisseur de solutions cloud](https://partner.microsoft.com/Solutions/cloud-reseller-overview) ou un [Accord Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) ?
Nous ne prenons actuellement pas en charge le changement d’offre vers un fournisseur de solutions cloud ou un Accord Entreprise dans le Centre des comptes. Pour inclure votre abonnement actuel dans un contrat Entreprise, demandez à votre administrateur d’inscription d’ajouter votre compte au contrat Entreprise. Vous recevez ensuite un e-mail d’invitation. Quand vous suivez les instructions pour accepter l’invitation, vos abonnements sont automatiquement rattachés au contrat Entreprise. À ce jour, aucune option ne permet de déplacer un abonnement existant vers un fournisseur de solutions cloud.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [gérer les rôles d’administrateur](billing-add-change-azure-subscription-administrator.md) pour votre abonnement
* Effectuez le suivi de votre utilisation en [téléchargeant les données d’utilisation et la facture](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help?-contact-support."></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

<!--HONumber=Oct16_HO2-->


