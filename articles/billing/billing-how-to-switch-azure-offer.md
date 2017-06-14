---
title: "Modification de votre offre d’abonnement Azure | Microsoft Docs"
description: "Découvrez comment changer votre abonnement Azure et basculer vers une autre offre à l’aide du portail de gestion des abonnements"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 9c4c872d03a3927d02d17de6cc2aae6e684f1fad
ms.openlocfilehash: b6a1e6175833ea0cdc785b18c88b43e06a7ad57c
ms.contentlocale: fr-fr
ms.lasthandoff: 02/13/2017


---
# <a name="switch-your-azure-subscription-to-another-offer"></a>Changer d’offre pour votre abonnement Azure
En tant que client [avec paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/), vous pouvez basculer votre abonnement Azure vers une autre offre du [Centre des comptes](https://account.windowsazure.com/Subscriptions). Par exemple, vous pouvez utiliser cette fonctionnalité pour tirer parti des [crédits mensuels pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si vous sollicitez un [essai gratuit](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau vers une offre de paiement à l’utilisation](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>Opérations prises en charge :
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
   
   ![Notez le bouton de changement d’offre sur le côté droit de la page](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. **Sélectionnez l’offre souhaitée** dans la liste des offres vers lesquelles il est possible de basculer votre abonnement. La liste varie en fonction des adhésions auxquelles est associé votre compte. Si rien n’est disponible, examinez la [liste des offres disponibles vers lesquelles il est possible de basculer](#whats-supported) et vérifiez que vous disposez des adhésions appropriées. 
   
   ![Sélectionnez une offre à laquelle vous souhaitez passer](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. Selon l’offre que vous envisagez d’adopter, une note peut apparaître sur l’impact du changement. Parcourez cette liste soigneusement et suivez les instructions avant de continuer.
   
   ![Passez en revue les notes](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. Vous pouvez renommer votre abonnement. Par défaut, nous le définissons sur le nom de la nouvelle offre. Cliquez sur **Changer d’offre** pour terminer le processus.
   
   ![Cliquez sur le bouton vert](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. C’est terminé ! Votre abonnement est désormais associé à la nouvelle offre.

## <a name="what-is-an-azure-offer"></a>Qu'est-ce qu'une offre Azure ?
Une offre Azure correspond au *type* d’abonnement Azure que vous avez. Par exemple, [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure dans Open](https://azure.microsoft.com/offers/ms-azr-0111p/) et [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) sont tous des offres Azure. Chaque offre a différents [termes](https://azure.microsoft.com/support/legal/offer-details/) et certaines présentent des avantages particuliers. Vous trouverez l’offre de votre abonnement dans la page d’abonnement Centre des comptes. Cliquez sur le nom de l’offre pour obtenir plus de détails.

   ![Cliquez sur le lien de l’offre dans le Centre des comptes pour obtenir plus de détails](./media/billing-how-to-switch-azure-offer/offerlink.png)

## <a name="why-cant-i-switch-offers"></a>Pourquoi ne puis-je pas changer les offres ?
Il est possible que le bouton **Changer pour une autre offre** ne s’affiche pas si :

* Vous n’avez pas souscrit à un modèle de [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Actuellement, seuls les abonnements de paiement à l’utilisation peuvent être basculés vers une autre offre.
  
  * Si vous sollicitez un [essai gratuit](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau vers une offre de paiement à l’utilisation](billing-upgrade-azure-subscription.md).
  * Pour basculer vers une offre d’un abonnement différent, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Vous êtes toujours dans votre première période de facturation ; vous devez attendre l’expiration de votre première période de facturation avant de pouvoir changer d’offre.

Le message **Il n’y a pas d’offres disponibles dans votre région ou dans votre pays à l’heure actuelle.** s’affiche si :

* Vous n’êtes pas éligible pour changer d’offre. Consultez la [liste des offres vers lesquelles vous pouvez basculer](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Quel impact le basculement des offres Azure a-t-il sur mon service et ma facturation ?
Voilà ce qui se passe lorsque vous changez de plans Azure dans le Centre des comptes.

### <a name="access-to-services"></a>Accéder aux services
Aucun temps d’arrêt de service n’est à déplorer pour les utilisateurs associés à l’abonnement. Toutefois, l’offre pour laquelle vous changez peut avoir des restrictions. Par exemple, certaines offres interdisent l’utilisation pour la production. Dans ce cas, il vous faudrait déplacer les ressources de production vers un autre abonnement.

### <a name="billing"></a>Facturation
Le jour où vous changez d’offre, une facture est générée pour l’ensemble des frais dus. Par la suite, votre abonnement est facturé en fonction des conditions de tarification de la nouvelle offre. La date de facturation de votre abonnement devient la date à laquelle vous avez changé d’offre. Les données d’utilisation et de facturation antérieures au changement d’offre n’étant pas conservées, nous vous recommandons d’en télécharger une copie avant le changement.

> [!NOTE]
> En raison de contraintes liées à la facturation, vous ne pouvez pas changer d’offre pendant le premier cycle de facturation qui suit la création d’un abonnement.
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Puis-je migrer du modèle de paiement à l’utilisation vers un [fournisseur de solutions cloud](https://partner.microsoft.com/Solutions/cloud-reseller-overview) ou un [Accord Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) ?

- Pour migrer vers un CSP consultez la section [Migration d’un abonnement Azure vers CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

- Pour migrer vers contrat Entreprise, demandez à votre administrateur d’inscription d’ajouter votre compte au contrat Entreprise. Suivez les instructions figurant dans l’e-mail d’invitation pour que vos abonnements soient déplacés vers l’inscription au contrat Entreprise. Pour plus d’informations, consultez [Associer un compte existant](https://ea.azure.com/helpdocs/associateExistingAccount) dans le portail EA.

## <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Puis-je migrer des données et des services vers un nouvel abonnement ?

- Vous pouvez migrer les ressources directement vers un nouvel abonnement. Consultez la rubrique [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

- Pour transférer la propriété d’un abonnement Azure et tout son contenu à une autre personne, consultez [Transfert de propriété d’un abonnement Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.


