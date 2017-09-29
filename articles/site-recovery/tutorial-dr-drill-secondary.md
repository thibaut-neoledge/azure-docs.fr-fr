---
title: "Exécuter une simulation de récupération d’urgence sur votre site local secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Découvrir comment exécuter une simulation de récupération d’urgence sur votre site local secondaire avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Exécuter une simulation de récupération d’urgence pour des machines virtuelles Hyper-V sur votre site local secondaire

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel montre comment exécuter une simulation de récupération d’urgence pour des machines virtuelles Hyper-V sur votre site local secondaire. Les machines virtuelles Hyper-V doivent être gérées dans un cloud privé System Center Virtual Machine Manager (VMM). Une simulation valide votre stratégie de réplication sans perte de données ou temps d’arrêt et n’affecte pas votre environnement de production. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer les conditions préalables pour le test de basculement
> * Exécuter un test de basculement pour une seule machine


## <a name="prerequisites"></a>Composants requis

- Vous pouvez exécuter un test de basculement avec quelques options de mise en réseau sur le site secondaire. Vous pouvez exécuter le basculement sans réseau, avec un réseau existant ou laisser Azure Site Recovery créer automatiquement un réseau de test. 
**Si vous souhaitez utiliser un réseau de production existant pour le test de basculement** :
    - La machine virtuelle principale doit être arrêtée lorsque vous effectuez le test de basculement. Si vous ne le faites pas, deux machines virtuelles avec la même identité s’exécuteront simultanément sur le même réseau. 
    - Si vous apportez des modifications aux machines virtuelles de test, ces changements sont perdus lors du nettoyage du test de basculement. Les modifications ne sont pas répliquées vers la machine virtuelle principale.
    - Tester un réseau de production entraîne une interruption des charges de travail de production. Demandez aux utilisateurs de ne pas utiliser d’applications associées lorsque la simulation de récupération d’urgence est en cours. 
- Le réseau de test de réplica n’a pas besoin de correspondre au type de réseau logique VMM utilisé pour le test de basculement. Toutefois, certaines combinaisons ne fonctionnent pas. Par exemple si le réplica utilise DHCP et une isolation basée sur un réseau local virtuel, vous ne pouvez pas utiliser une virtualisation de réseau Windows pour le réseau de test de basculement, car elle a besoin de pools d’adresses IP. 
- Nous vous recommandons de ne pas utiliser pour test de basculement le réseau que vous avez sélectionné pour le mappage réseau.


## <a name="run-a-test-failover-for-a-vm"></a>Exécuter un test de basculement pour une machine virtuelle

1. Dans **Éléments répliqués**, cliquez sur la machine virtuelle, puis sur **Test de basculement**.
2. Dans **Test de basculement**, indiquez le mode de connexion des machines virtuelles aux réseaux après le test de basculement. Pour les besoins de ce didacticiel, nous vous recommandons de laisser Site Recovery créer automatiquement un réseau de test. [Plus d’informations](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)
3. Cliquez sur **OK** pour commencer le basculement. Effectuez un suivi de l’opération dans l’onglet **Tâches** .
4. Une fois le basculement terminé, vérifiez que les machines virtuelles démarrent correctement.
5. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement**. Cette opération supprime les machines virtuelles de test et les réseaux créés pendant le test de basculement.
6. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement. 


## <a name="next-steps"></a>Étapes suivantes

[Exécuter un basculement de production](tutorial-vmm-to-vmm-failover-failback.md)







