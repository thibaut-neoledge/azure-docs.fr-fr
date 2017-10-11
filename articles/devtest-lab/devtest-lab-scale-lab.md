---
title: "Limites et quotas de mise à l’échelle dans votre laboratoire Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment mettre à l’échelle un laboratoire dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Limites et quotas de mise à l’échelle dans votre laboratoire Azure DevTest Labs
Lorsque vous travaillez dans DevTest Labs, vous pouvez remarquer un certain nombre de limites par défaut qui s’appliquent à certaines ressources Azure, ce qui peut affecter le service DevTest Labs. Ces limites sont appelées **quotas**.

> [!NOTE]
> Le service DevTest Labs n’impose aucun quota. Les quotas que vous pouvez rencontrer sont les contraintes par défaut de l’abonnement Azure global.

Vous pouvez utiliser chaque ressource Azure jusqu’à ce que vous ayez atteint son quota. Chaque abonnement a des quotas distincts et l’utilisation est suivie par abonnement.

Par exemple, chaque abonnement possède un quota par défaut de 20 cœurs. Par conséquent, si vous créez des machines virtuelles dans votre laboratoire avec quatre cœurs, vous pouvez uniquement créer cinq machines virtuelles. 

La page [Abonnement Azure et limites de service](https://docs.microsoft.com/azure/azure-subscription-service-limits) dresse la liste des quotas les plus courants pour les ressources Azure. Les ressources couramment utilisées dans un laboratoire, et pour lesquelles vous pouvez rencontrer des quotas, incluent les noyaux de machine virtuelle, les adresses IP publiques, l’interface réseau, les disques managés, l’attribution de rôle RBAC et les circuits ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Voir votre utilisation et les quotas
Ces étapes vous montrent comment afficher les quotas actuels de votre abonnement pour des ressources Azure spécifiques, ainsi que le pourcentage utilisé pour chaque quota.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Autres services**, puis **Facturation** dans la liste.
1. Dans le panneau Facturation, sélectionnez un abonnement.
4. Sélectionnez **Utilisation + quotas**.

   ![Bouton Utilisation et quotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Le panneau Utilisation + quotas s’affiche. Il répertorie les différentes ressources disponibles dans cet abonnement et le pourcentage de quota utilisé par la ressource.

   ![Quotas et utilisation](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Demander plus de ressources dans votre abonnement
Si vous atteignez le plafond d’un quota, la limite par défaut d’une ressource dans un abonnement peut être augmentée jusqu’à une limite maximale, comme décrit dans [Abonnement Azure et limites de service](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Ces étapes vous montrent comment demander une augmentation du quota au moyen du [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Autres services**, **Facturation**, puis **Utilisation + quotas**.
1. Dans le panneau Utilisation + quotas, sélectionnez le bouton **Demander une augmentation**.

   ![Bouton Demander une augmentation](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Pour finaliser et envoyer la demande, remplissez les informations requises sous les trois onglets du formulaire **Nouvelle demande de support**.

   ![Formulaire Demander une augmentation](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

La page [Understanding Azure Limits and Increases (Présentation des limites et des augmentations Azure)](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) vous explique plus en détail comment contacter le support Azure pour demander une augmentation du quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Étapes suivantes
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
