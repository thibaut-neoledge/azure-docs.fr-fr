---
title: "Recommandations du conseiller Azure en matière de haute disponibilité | Microsoft Docs"
description: "Utilisez le conseiller Azure pour améliorer la haute disponibilité de vos déploiements Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 91b6fec93d4226af755be553e544a4ea7b1081ca

---

# <a name="advisor-high-availability-recommendations"></a>Recommandations du conseiller en matière de haute disponibilité

Le conseiller vous aide à garantir et à améliorer la continuité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de haute disponibilité auprès du conseiller à partir de l’onglet **Haute disponibilité** du tableau de bord du conseiller.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>Machines virtuelles dépourvues d’un groupe à haute disponibilité

Le conseiller identifie les machines virtuelles qui ne font pas partie d’un groupe à haute disponibilité et recommande de les déplacer dans un groupe à haute disponibilité. Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non et répondra au contrat de niveau de service Azure. Vous pouvez choisir de créer un groupe à haute disponibilité pour la machine virtuelle ou d’ajouter la machine virtuelle à un groupe à haute disponibilité existant.

> [!NOTE]
> Si vous choisissez de créer un groupe à haute disponibilité, vous devez ajouter au moins une machine virtuelle supplémentaire dans ce groupe à haute disponibilité après sa création. Nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité pour garantir que l’une d’elles reste disponible en cas de panne.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>Groupes à haute disponibilité avec une seule machine virtuelle 

Le conseille identifie les groupes à haute disponibilité contenant une seule machine virtuelle et recommande d’y ajouter au moins une machine virtuelle. Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez soit créer une machine virtuelle, soit utiliser une machine virtuelle existante et l’ajouter à un groupe à haute disponibilité.  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>Machines virtuelles avec des disques Standard

Le conseiller identifie les machines virtuelles avec des disques Standard et recommande la mise à niveau vers des disques Premium.  
Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent Premium Storage stockent les données sur des disques SSD. Nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’IOPS dans Azure Premium Storage pour que votre application bénéficie de performances optimales. Si votre disque ne nécessite pas une valeur élevée d’E/S par seconde, vous pouvez limiter les coûts en le maintenant en stockage standard. Le stockage standard stocke les données de disque des machines virtuelles sur des disques durs (HDD) au lieu de disques SSD. Vous pouvez choisir de migrer vos disques de machines virtuelles vers des disques Premium. Les disques Premium sont pris en charge sur la plupart des références de machines virtuelles. Toutefois, dans certains cas, si vous souhaitez utiliser des disques Premium, vous devrez également mettre à niveau la référence de votre machine virtuelle.   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de haute disponibilité dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, cliquez sur **Plus de services**, dans le volet de menu service, faites défiler jusqu’à **Surveillance et gestion**, puis cliquez sur **Conseiller Azure**. Cette opération permet de lancer le tableau de bord du conseiller. 
3. Dans le tableau de bord du conseiller, cliquez sur l’onglet **Haute disponibilité**, et sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations.

> [!NOTE]
> Le conseiller génère des recommandations pour les abonnements pour lesquels le rôle de **propriétaire, de collaborateur ou de lecteur** vous a été affecté.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations du conseiller, consultez les ressources suivantes :
-  [Présentation du conseiller Azure](advisor-overview.md)
-  [Prise en main du conseiller](advisor-get-started.md)
-  [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
-  [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
-  [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->


