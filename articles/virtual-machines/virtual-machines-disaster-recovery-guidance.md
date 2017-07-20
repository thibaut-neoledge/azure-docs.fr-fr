---
title: "Scénarios de récupération d’urgence des machines virtuelles Azure | Microsoft Docs"
description: "Découvrez quoi faire si une interruption du service Azure affecte de machines virtuelles Azure."
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: fb986a41e33501ee71c93a48457ac4114e33c671
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Que faire si une interruption du service Azure affecte des machines virtuelles Azure ?
Microsoft s’engage à déployer tous les efforts nécessaires pour vous garantir en permanence la disponibilité de ses services quand vous en avez besoin. Il arrive parfois que des phénomènes incontrôlables entraînent des interruptions de service non planifiées.

Microsoft fournit un Contrat de niveau de service (SLA) pour ses services en guise d’engagement en matière de disponibilité et de connectivité. Le contrat de niveau de service des différents services Azure se trouve à la page [Contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure dispose déjà de nombreuses fonctionnalités intégrées de plateforme qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article aborde un scénario réel de récupération d’urgence, dans lequel une région entière connaît une panne en raison d’une catastrophe naturelle majeure ou d’une interruption de service importante. Bien que ces cas soient rares, vous devez envisager l’éventualité d’une panne affectant l’ensemble d’une région. Si une région entière est confrontée à une interruption de service, les copies localement redondantes de vos données sont temporairement indisponibles. Si vous avez activé la géoréplication, trois copies supplémentaires de vos tables et objets blob Azure Storage sont stockées dans une autre région. En cas de panne régionale totale ou de sinistre rendant la région primaire irrécupérable, Azure remappe toutes les entrées DNS sur la région géorépliquée.

Pour vous aider à gérer ces rares occurrences, nous vous fournissons les conseils suivants pour les machines virtuelles Azure dans le cas d’une interruption de service sur l’ensemble de la région où votre application Azure Virtual Machine est déployée.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Option 1 : Lancer un basculement à l’aide d’Azure Site Recovery
Vous pouvez configurer Azure Site Recovery pour vos machines virtuelles afin de pouvoir récupérer votre application en un seul clic en quelques minutes. Vous pouvez répliquer vers la région Azure de votre choix sans être limité à des régions appairées. Vous pouvez commencer par [répliquer vos machines virtuelles](https://aka.ms/a2a-getting-started). Vous pouvez [créer un plan de récupération](../site-recovery/site-recovery-create-recovery-plans.md) pour pouvoir automatiser la totalité du processus de basculement de votre application. Vous pouvez [tester vos basculements](../site-recovery/site-recovery-test-failover-to-azure.md) à l’avance sans affecter l’application de production ou la réplication en cours. En cas d’indisponibilité de la région principale, vous devez simplement [lancer un basculement](../site-recovery/site-recovery-failover.md) et mettre votre application dans la région cible.


## <a name="option-2-wait-for-recovery"></a>Option 2 : Attendre la récupération
Dans ce cas, aucune action n’est requise de votre part. Soyez assuré que nous travaillons assidûment à la restauration de la disponibilité du service. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

Cette option est la meilleure si vous n’avez pas activé Azure Site Recovery, le stockage géoredondant avec accès en lecture ou le stockage géoredondant avant l’interruption. Si vous avez configuré le stockage géoredondant ou le stockage géoredondant avec accès en lecture pour le compte de stockage où sont stockés les disques durs virtuels de vos machines virtuelles, vous pouvez essayer de récupérer le disque dur virtuel de l’image de base et l’utiliser pour tenter d’approvisionner une nouvelle machine virtuelle. Cette option n’est pas recommandée, car elle ne garantit pas la synchronisation des données. Par conséquent, le fonctionnement de cette option n’est pas garanti.


> [!NOTE]
> N’oubliez pas que vous n’avez aucun contrôle sur ce processus et qu’il ne se produit que pour des interruptions du service au niveau régional. Ainsi, vous devez également vous appuyer sur d’autres stratégies de sauvegarde propres à l’application pour atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section consacrée aux [stratégies de données pour une récupération d’urgence](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Étapes suivantes

- Commencez à [protéger vos applications s’exécutant sur des machines virtuelles Azure ](https://aka.ms/a2a-getting-started) en utilisant Azure Site Recovery

- Pour plus d’informations sur la façon d’implémenter une stratégie de récupération d’urgence et de haute disponibilité, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Pour une compréhension technique détaillée des fonctionnalités de la plateforme cloud, consultez le [Guide technique de la résilience Azure](../resiliency/resiliency-technical-guidance.md).


- Si les instructions ne sont pas claires ou que vous souhaitez que Microsoft effectue les opérations en votre nom, contactez le [service clientèle](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

