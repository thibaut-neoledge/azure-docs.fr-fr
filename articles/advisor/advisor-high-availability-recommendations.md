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
ms.openlocfilehash: e1cd7948e1969cd4ddb926e428c09b559190a805
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-high-availability-recommendations"></a>Recommandations du conseiller en matière de haute disponibilité

Azure Advisor vous aide à garantir et à améliorer la continuité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de haute disponibilité auprès du conseiller à partir de l’onglet **Haute disponibilité** du tableau de bord du conseiller.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Assurer la tolérance de panne des machines virtuelles

Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Le conseiller identifie les machines virtuelles qui ne font pas partie d’un groupe à haute disponibilité et recommande de les déplacer dans un groupe à haute disponibilité. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez choisir de créer un groupe à haute disponibilité pour la machine virtuelle ou d’ajouter la machine virtuelle à un groupe à haute disponibilité existant.

> [!NOTE]
> Si vous choisissez de créer un groupe à haute disponibilité, vous devez y ajouter au moins une machine virtuelle supplémentaire. Nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité pour garantir qu’au moins une d’elles reste disponible en cas de panne.

## <a name="ensure-availability-set-fault-tolerance"></a>Assurer la tolérance de panne d’un groupe à haute disponibilité 

Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Advisor identifie les groupes à haute disponibilité contenant une seule machine virtuelle et recommande d’y ajouter au moins une machine virtuelle. Cette configuration assure qu’au moins une des machines virtuelles est disponible pendant un événement de maintenance planifié ou non et répond au contrat de niveau de service Azure. Vous pouvez créer une machine virtuelle ou ajouter une machine virtuelle existante au groupe à haute disponibilité.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Assurer la tolérance de panne d’une passerelle
Pour garantir la continuité métier des applications stratégiques qui sont alimentées par des passerelles d’application, Advisor identifie les instances de passerelle d’application qui ne sont pas configurées pour la tolérance de panne, et suggère des mesures de mise à jour que vous pouvez prendre. Advisor identifie les passerelles d’application de moyenne ou grande taille à instance unique, et recommande d’ajouter au moins une instance de plus. Il identifie également les petites passerelles d’application à une ou plusieurs instances et recommande de migrer vers des références de moyenne ou grande taille. Advisor recommande ces actions pour assurer que vos instances de passerelle d’application sont configurées pour satisfaire les exigences actuelles du contrat de niveau de service pour ces ressources.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Améliorer les performances et la fiabilité des disques de machine virtuelle

Advisor identifie les machines virtuelles avec des disques Standard et recommande la mise à niveau vers des disques Premium.
 
Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent des comptes Premium Storage stockent les données sur des disques SSD. Pour que votre application bénéficie de performances optimales, nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’E/S par seconde dans le stockage Premium. 

Si votre disque ne nécessite pas une valeur élevée d’E/S par seconde, vous pouvez limiter les coûts en le maintenant en stockage standard. Le stockage standard stocke les données de disque des machines virtuelles sur des disques durs (HDD) au lieu de disques SSD. Vous pouvez choisir de migrer vos disques de machines virtuelles vers des disques Premium. Les disques Premium sont pris en charge sur la plupart des références de machines virtuelles. Toutefois, dans certains cas, si vous souhaitez utiliser des disques Premium, vous devrez également mettre à niveau la référence de votre machine virtuelle.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Protégez les données de vos machines virtuelles d’une suppression accidentelle
La configuration de la sauvegarde de machine virtuelle permet de garantir la disponibilité de vos données métier stratégiques et offre une protection contre une suppression accidentelle ou un endommagement des données.  Advisor identifie les machines virtuelles sur lesquelles la sauvegarde n’est pas activée, et recommande l’activation de la sauvegarde. 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de haute disponibilité dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Haute disponibilité**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Prise en main du conseiller](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)

