---
title: "Recommandations du conseiller Azure en matière de haute disponibilité | Microsoft Docs"
description: "Utilisez le conseiller Azure pour améliorer la haute disponibilité de vos déploiements Azure."
services: advisor
documentationcenter: NA
author: KumudD
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 23c159471a6e5a7ad9cb545840e8afd3ac72ecba
ms.contentlocale: fr-fr
ms.lasthandoff: 03/31/2017

---

# <a name="advisor-high-availability-recommendations"></a>Recommandations du conseiller en matière de haute disponibilité

Azure Advisor vous aide à garantir et à améliorer la continuité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de haute disponibilité auprès du conseiller à partir de l’onglet **Haute disponibilité** du tableau de bord du conseiller.

![Bouton de haute disponibilité sur le tableau de bord d’Advisor](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Assurer la tolérance de panne des machines virtuelles

Le conseiller identifie les machines virtuelles qui ne font pas partie d’un groupe à haute disponibilité et recommande de les déplacer dans un groupe à haute disponibilité. Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez choisir de créer un groupe à haute disponibilité pour la machine virtuelle ou d’ajouter la machine virtuelle à un groupe à haute disponibilité existant.

> [!NOTE]
> Si vous choisissez de créer un groupe à haute disponibilité, vous devez y ajouter au moins une machine virtuelle supplémentaire. Nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité pour garantir qu’au moins une d’elles reste disponible en cas de panne.

![Recommandation d’Advisor : Pour la redondance des machines virtuelles, utilisez les groupes à haute disponibilité](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Assurer la tolérance de panne d’un groupe à haute disponibilité 

Advisor identifie les groupes à haute disponibilité contenant une seule machine virtuelle et recommande d’y ajouter au moins une machine virtuelle. Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez soit créer une machine virtuelle, soit utiliser une machine virtuelle existante et l’ajouter à un groupe à haute disponibilité.  

![Recommandation d’Advisor : Ajoutez une ou plusieurs machines virtuelles à ce groupe à haute disponibilité](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Assurer la tolérance de panne d’une passerelle
Pour garantir la continuité métier des applications stratégiques qui sont alimentées par des passerelles d’application, Advisor identifie les instances de passerelle d’application qui ne sont pas configurées pour la tolérance de panne, et suggère des mesures de mise à jour que vous pouvez prendre. Advisor identifie les passerelles d’application de moyenne ou grande taille à instance unique, et recommande d’ajouter au moins une instance de plus. Il identifie également les petites passerelles d’application à une ou plusieurs instances et recommande de migrer vers des références de moyenne ou grande taille. Advisor recommande ces actions pour assurer que vos instances de passerelle d’application sont configurées pour satisfaire les exigences actuelles du contrat de niveau de service pour ces ressources.

![Recommandation d’Advisor : Déployez deux instances (ou plus) de passerelle d’application de moyenne ou grande taille](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Améliorer les performances et la fiabilité des disques de machine virtuelle

Advisor identifie les machines virtuelles avec des disques Standard et recommande la mise à niveau vers des disques Premium.
 
Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent des comptes Premium Storage stockent les données sur des disques SSD. Pour que votre application bénéficie de performances optimales, nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’E/S par seconde dans le stockage Premium. 

Si votre disque ne nécessite pas une valeur élevée d’E/S par seconde, vous pouvez limiter les coûts en le maintenant en stockage standard. Le stockage standard stocke les données de disque des machines virtuelles sur des disques durs (HDD) au lieu de disques SSD. Vous pouvez choisir de migrer vos disques de machines virtuelles vers des disques Premium. Les disques Premium sont pris en charge sur la plupart des références de machines virtuelles. Toutefois, dans certains cas, si vous souhaitez utiliser des disques Premium, vous devrez également mettre à niveau la référence de votre machine virtuelle.

![Recommandation d’Advisor : Améliorez la fiabilité de vos disques virtuels en mettant à niveau vers des disques premium](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Protégez les données de vos machines virtuelles d’une suppression accidentelle
Advisor identifie les machines virtuelles sur lesquelles la sauvegarde n’est pas activée, et recommande l’activation de la sauvegarde. La configuration de la sauvegarde de machine virtuelle permet de garantir la disponibilité de vos données métier stratégiques et offre une protection contre une suppression accidentelle ou un endommagement des données.

![Recommandation d’Advisor : Configurez la sauvegarde des machines virtuelles pour protéger vos données stratégiques](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Accéder aux recommandations en matière de haute disponibilité dans Advisor

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le volet gauche, cliquez sur **Plus de services**.

3. Dans le volet du menu de services, sous **Surveillance et gestion**, cliquez sur **Azure Advisor**.  
 Le tableau de bord Advisor s’affiche.

4. Dans le tableau de bord Advisor, cliquez sur l’onglet **Haute disponibilité**, et sélectionnez l’abonnement pour lequel vous souhaitez recevoir des recommandations.

> [!NOTE]
> Pour accéder aux recommandations d’Advisor, vous devez d’abord *enregistrer* votre abonnement avec auprès d’Advisor. Un abonnement est enregistré lorsque son *propriétaire* lance le tableau de bord d’Advisor puis clique sur le bouton **Obtenir des recommandations**. Cette opération ne doit être *exécutée qu’une seule fois*. Une fois l’abonnement enregistré, vous pouvez accéder aux recommandations d’Advisor en tant que *propriétaire*, *collaborateur*, ou *lecteur* d’un abonnement, d’un groupe de ressources ou d’une ressource spécifique.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Prise en main du conseiller](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)


