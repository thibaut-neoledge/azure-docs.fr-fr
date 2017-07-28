---
title: "Questions fréquentes (FAQ) concernant les problèmes de déploiement pour Microsoft Azure Cloud Services | Microsoft Docs"
description: "Cet article répertorie les questions fréquentes sur le déploiement pour Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 9b788b1d95c821a4bb76cd4dea1d689d36e2f92b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017


---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problèmes de déploiement pour Azure Cloud Services : questions fréquentes (FAQ)

Cet article comprend des questions fréquentes sur les problèmes de déploiement pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Pourquoi le déploiement d’un service cloud dans l’emplacement intermédiaire échoue-t-il parfois avec une erreur d’allocation de ressources si l’emplacement de production contient déjà un déploiement ?
Si un service cloud est déployé à chaque emplacement, l’ensemble du service cloud est épinglé à un cluster spécifique. Par conséquent, si l’emplacement de production contient déjà un déploiement, un nouveau déploiement intermédiaire peut être affecté uniquement dans le même cluster que l’emplacement de production.

Des échecs d’allocation se produisent quand le cluster dans lequel se situe votre service cloud n’a pas suffisamment de ressources de calcul physiques pour satisfaire votre demande de déploiement.

Pour savoir comment limiter les effets de ces échecs d’allocation, consultez [Échec d’allocation d’un service cloud : solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Pourquoi une montée en puissance ou une augmentation de la taille des instances dans le déploiement d’un service cloud génère-t-elle parfois un échec d’allocation ?
Quand un service cloud est déployé, il est généralement épinglé à un cluster spécifique. Autrement dit, quand un service cloud existant fait l’objet d’une montée en puissance/augmentation de la taille des instances, les nouvelles instances doivent être allouées au même cluster. Si le cluster est proche de sa capacité maximale ou que la taille/type de machine virtuelle souhaité n’est pas disponible, la demande peut échouer.

Pour savoir comment limiter les effets de ces échecs d’allocation, consultez [Échec d’allocation d’un service cloud : solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Pourquoi le déploiement d’un service cloud dans un groupe d’affinités provoque-t-il parfois un échec d’allocation ?
Un nouveau déploiement dans un service cloud vide peut être alloué par l’infrastructure d’un cluster de cette région, sauf si le service cloud est épinglé à un groupe d’affinités. Des déploiements vers le même groupe d'affinités seront tentés sur le même cluster. Si le cluster est presque plein, la demande peut échouer.

Pour savoir comment limiter les effets de ces échecs d’allocation, consultez [Échec d’allocation d’un service cloud : solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Pourquoi le fait de modifier la taille des machines virtuelles ou d’ajouter une nouvelle machine virtuelle à un service cloud existant occasionne-t-il parfois un échec d’allocation ?
Les clusters d’un centre de données peuvent présenter différentes configurations de types de machines (par exemple, série A, série Av2, série D, série Dv2, série G, série H, etc.). Mais les clusters n’ont pas nécessairement besoin d’être tous dotés de tous les types de machines virtuelles. Par exemple, si vous essayez d’ajouter une machine virtuelle de série D à un service cloud qui est déjà déployé dans un cluster constitué uniquement de machines de série A, vous rencontrerez un échec d’allocation. Il en est de même si vous essayez de modifier la taille des références de machines virtuelles (par exemple, en passant d’une série A à une série D).

Pour savoir comment limiter les effets de ces échecs d’allocation, consultez [Échec d’allocation d’un service cloud : solutions](cloud-services-allocation-failures.md#solutions).

Pour vérifier les tailles disponibles dans votre région, consultez [Microsoft Azure : Disponibilité des produits par région](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Pourquoi le déploiement d’un service cloud échoue-t-il parfois en raison des limites/quotas/contraintes de mon abonnement ou service ?
Le déploiement d’un service cloud peut échouer si les ressources qui doivent être allouées dépassent la valeur par défaut ou le quota maximal autorisé pour votre service au niveau de la région et/ou du centre de données. Pour plus d’informations, consultez [Limites de services cloud](../azure-subscription-service-limits.md#cloud-services-limits).

Vous pouvez aussi suivre l’utilisation et/ou le quota actuels de votre abonnement sur le portail : Portail Azure=> Abonnements => \<abonnement approprié>=> « Utilisation + quota ».

Vous pouvez aussi obtenir des informations sur l’utilisation/consommation de ressources via les API de facturation Azure. Consultez [API Azure Resource Usage (version préliminaire)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Comment modifier la taille de machine virtuelle d’un service cloud déployé sans le redéployer ?
Vous ne pouvez pas modifier la taille de machine virtuelle d’un service cloud déployé sans le redéployer. La taille de machine virtuelle est intégrée au fichier CSDEF, dont la mise à jour n’est possible que moyennant un redéploiement.

Pour plus d’informations, consultez [Comment mettre à jour un service clou](cloud-services-update-azure-service.md).

 

