---
title: "Exécuter un test de basculement pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire avec Azure Site Recovery | Documents Microsoft"
description: "Décrit comment exécuter un test de basculement pour la réplication de machines virtuelles Hyper-V sur un site secondaire System Center VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>Étape 10 : exécuter un test de basculement pour la réplication Hyper-V vers un site secondaire


Après avoir activé la réplication de machines virtuelles Hyper-V avec [Azure Site Recovery](site-recovery-overview.md), cet article permet d’exécuter un test de basculement. Un test de basculement vérifie que la réplication fonctionne, sans affecter votre environnement de production. 


Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Avant de commencer

- Lors du déclenchement d’un test de basculement, vous pouvez spécifier le réseau auquel les machines virtuelles réplica de test se connectent. [En savoir plus](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery) sur les options de réseau.
- Nous vous recommandons de ne pas choisir un réseau que vous avez sélectionné lors du mappage réseau.
- Les instructions fournies dans cet article décrivent comment basculer d’une seule machine virtuelle. Reportez-vous à la documentation sur la[création de plans de récupération](site-recovery-create-recovery-plans.md) si vous souhaitez basculer ensemble plusieurs machines virtuelles.
- En savoir plus sur les[limitations des tests de basculement](site-recovery-test-failover-vmm-to-vmm.md#things-to-note).
- L’adresse IP donnée à une machine virtuelle durant un test de basculement est identique à l’adresse IP qu’elle recevrait durant un basculement planifié ou non planifié (à condition que l’adresse IP soit disponible dans le réseau de test de basculement). Si l’adresse IP n’est pas disponible dans le réseau de test de basculement, la machine virtuelle reçoit une autre adresse IP disponible dans le réseau de test de basculement.
- Si vous ne souhaitez pas effectuer un test de basculement dans votre réseau de production, pour une validation complète de l’ordinateur de connectivité réseau de bout en bout, sachez que :
    - La machine virtuelle principale doit être arrêtée lorsque vous effectuez le test de basculement. Si vous ne le faites pas, deux machines virtuelles avec la même identité s’exécuteront sur le même réseau en même temps. 
    - Si vous apportez des modifications aux machines virtuelles de test, ces modifications sont perdues lors du nettoyage du test de basculement. Ces modifications ne sont pas répliquées vers la machine virtuelle principale.
    - Tester un réseau de production entraîne une interruption des charges de travail de production. Demandez aux utilisateurs de ne pas utiliser l’application lorsque l’extraction de récupération d’urgence est en cours d’exécution.  


## <a name="run-a-test-failover-for-a-vm"></a>Exécuter un test de basculement pour une machine virtuelle

1. Pour effectuer le basculement d’une seule machine virtuelle, dans **Éléments répliqués**, cliquez sur la machine virtuelle > **Test de basculement**.
2. Dans **Test de basculement**, indiquez le mode de connexion des machines virtuelles aux réseaux après le test de basculement. 
3. Cliquez sur **OK** pour commencer le basculement. Effectuez un suivi de l’opération dans l’onglet **Tâches** .
5. Une fois le basculement terminé, vérifiez que les machines virtuelles démarrent correctement.
6. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement** sur le plan de récupération.
7. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement. Cette étape supprime les machines et les réseaux virtuels qui ont été créés au cours du test de basculement.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez testé le déploiement, apprenez-en davantage sur les autres types de [basculement](site-recovery-failover.md).

