---
title: "Configurer VMM et Hyper-V pour la réplication sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Explique comment configurer des serveurs System Center VMM et des hôtes Hyper-V pour la réplication sur un site VMM secondaire."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>Étape 4 : Configurer VMM et Hyper-V pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire 

Après avoir préparé la mise en réseau, configurez les serveurs System Center VMM et les hôtes Hyper-V pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire à l’aide de [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure. 

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="prepare-vmm-servers"></a>Préparer les serveurs VMM 

Pour préparer le déploiement :


1. Veillez à ce que les serveurs VMM respectent les [exigences de support](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) et les [conditions préalables de déploiement](vmm-to-vmm-walkthrough-prerequisites.md).
2. Veillez à ce que les serveurs VMM soient connectés à Internet et puissent accéder à ces URL.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.
    - Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).
3. Veillez à ce que le serveur VMM soit [préparé pour le mappage réseau](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping).


## <a name="prepare-hyper-v-hostsclusters"></a>Préparer les hôtes et clusters Hyper-V

1. Veillez à ce que ce que les hôtes et clusters Hyper-V respectent les [exigences de support](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) et les [conditions préalables de déploiement](vmm-to-vmm-walkthrough-prerequisites.md).
2. Vérifiez les exigences des [machines virtuelles Hyper-V](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
3. Vérifiez les exigences du [réseau](site-recovery-support-matrix-to-sec-site.md#network-configuration) et du [stockage](site-recovery-support-matrix-to-sec-site.md#storage).
4. Veillez à ce que les hôtes Hyper-V soient connectés à Internet et aient accès à ces URL.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.
    - Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).

## <a name="prepare-for-single-server-deployment"></a>Préparation du déploiement d’un serveur unique


Si vous n’avez qu’un seul serveur VMM, vous pouvez répliquer des machines virtuelles dans des hôtes Hyper-V du cloud VMM vers [Azure](hyper-v-site-walkthrough-overview.md) ou vers un cloud VMM secondaire, comme expliqué dans ce document. Nous vous recommandons la première option, dans la mesure où la réplication entre les clouds n’est pas transparente.

Si vous ne souhaitez pas répliquer entre les clouds, vous pouvez le faire avec un seul serveur VMM autonome ou avec un seul serveur VMM déployé dans un cluster Windows étendu.

### <a name="replicate-with-a-standalone-vmm-server"></a>Réplication avec un serveur VMM autonome

Dans ce scénario, vous déployez le serveur VMM unique comme machine virtuelle sur le site principal et répliquez cette machine virtuelle sur un site secondaire avec Site Recovery et le réplica Hyper-V.

1. **Configurez VMM sur une machine virtuelle Hyper-V**. Nous vous conseillons de stocker l’instance SQL Server utilisée par VMM sur la même machine virtuelle. De cette manière, vous pouvez gagner du temps, car une seule machine virtuelle doit être créée. Si vous souhaitez utiliser une instance distante de SQL Server, vous devez récupérer cette instance pour pouvoir récupérer VMM en cas de défaillance.
2. **Vérifiez que le serveur VMM a au moins deux clouds configurés**. Un cloud contient les machines virtuelles à répliquer et l’autre cloud sert d’emplacement secondaire. Le cloud qui contient les machines virtuelles que vous souhaitez protéger doit remplir les [conditions préalables](#prerequisites).
3. Configurez Site Recovery comme le décrit cet article. Créez et inscrivez le serveur VMM dans un coffre, configurez une stratégie de réplication et activez la réplication. Les noms source et cible VMM sont alors identiques. Spécifiez que la réplication initiale s’effectue via le réseau.
4. Lorsque vous configurez le mappage réseau, vous mappez le réseau de machines virtuelles du cloud principal au réseau de machines virtuelles du cloud secondaire.
5. Dans la console Gestionnaire Hyper-V, activez la réplication Hyper-V sur l’hôte Hyper-V qui contient la machine virtuelle VMM et activez la réplication sur la machine virtuelle. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à des clouds protégés par Site Recovery afin de garantir que les paramètres de réplication Hyper-V ne sont pas remplacés par Site Recovery.
6. Si vous créez des plans de récupération pour le basculement, vous utilisez le même serveur VMM source et cible.
7. Lors d’une panne complète, vous effectuez le basculement et la récupération comme suit :

   1. Dans la console Hyper-V Manager sur le site secondaire, exécutez un basculement non planifié pour basculer la machine virtuelle VMM du site principal vers le site secondaire.
   2. Vérifiez que la machine virtuelle VMM est opérationnelle et dans le coffre, puis exécutez un basculement non planifié des machines virtuelles du cloud principal vers le cloud secondaire. Validez le basculement, puis sélectionnez un autre point de récupération si nécessaire.
   3. Une fois que le basculement non planifié est terminé, l’utilisateur peut à nouveau accéder à toutes les ressources sur le site principal.
   4. Lorsque le site principal est de nouveau disponible, dans la console du gestionnaire Hyper-V du site secondaire, activez la réplication inverse de la machine virtuelle VMM. La réplication de la machine virtuelle du site secondaire au site principal démarre.
   5. Exécutez un basculement planifié dans la console du gestionnaire Hyper-V du site secondaire, afin de basculer la machine virtuelle VMM vers le site principal. Validez le basculement. Activez ensuite la réplication inverse, de manière à ce que la machine virtuelle VMM soit de nouveau répliquée du site principal vers le site secondaire.
   6. Dans le coffre Recovery Services, activez la réplication inverse pour les machines virtuelles de charge de travail pour démarrer la réplication du site secondaire vers le site principal.
   7. Dans le coffre Recovery Services, exécutez un basculement planifié pour restaurer les machines virtuelles de charge de travail vers le site principal. Validez le basculement pour le terminer. Ensuite, activez la réplication inverse pour commencer la réplication des machines virtuelles de charge de travail du site principal vers le site secondaire.

### <a name="replicate-with-a-stretched-vmm-cluster"></a>Réplication avec un cluster VMM étiré

Au lieu de déployer un serveur VMM autonome en tant que machine virtuelle répliquée vers un site secondaire, vous pouvez rendre VMM hautement disponible en le déployant comme machine virtuelle dans un cluster de basculement Windows. pour fournir une résilience de la charge de travail et une protection contre les défaillances matérielles. Pour effectuer un déploiement avec Site Recovery, la machine virtuelle VMM doit être déployée dans un cluster étendu sur des sites géographiquement distincts. Pour ce faire :

1. Installez VMM sur des machines virtuelles dans un cluster de basculement Windows et sélectionnez l’option permettant d’exécuter le serveur comme étant hautement disponible lors de configuration.
2. L’instance SQL Server utilisée par VMM doit être répliquée avec des groupes de disponibilité SQL Server AlwaysOn pour qu’un réplica de la base de données se trouve sur le site secondaire.
3. Suivez les instructions de cet article pour créer un coffre, inscrire le serveur et configurer la protection. Vous devez inscrire chaque serveur VMM dans le cluster du coffre Recovery Services. Pour ce faire, vous installez le fournisseur sur un nœud actif et inscrivez le serveur VMM. Vous installez ensuite le fournisseur sur les autres nœuds.
4. En cas de défaillance, le serveur VMM et la base de données SQL Server correspondante sont basculés et accessibles sur le site secondaire.



## <a name="next-steps"></a>Étapes suivantes

Accédez à la section [Étape 5 : Configurer un coffre](vmm-to-vmm-walkthrough-create-vault.md).
