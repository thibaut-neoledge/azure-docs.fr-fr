---
title: "Exécuter un test de basculement pour la réplication des machines virtuelles Azure avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes requises pour exécuter un test de basculement pour la réplication des machines virtuelles Azure vers une autre région Azure à l’aide du service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>Étape 6 : Exécuter un test de basculement pour la réplication des machines virtuelles Azure

Une fois que vous avez activé la réplication pour les machines virtuelles Azure, suivez les étapes de cet article pour exécuter un test de basculement depuis une région Azure vers une autre à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

- À la fin de cet article, vous devrez avoir vérifié avec un test de basculement qu’au moins une machine virtuelle Azure peut basculer vers votre région Azure secondaire. 
- Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>
> La réplication de machines virtuelles Azure est disponible en préversion.


## <a name="before-you-start"></a>Avant de commencer

- Avant d’exécuter un test de basculement, nous vous recommandons de vérifier les propriétés de la machine virtuelle et d’apporter toutes les modifications requises. Vous pouvez accéder aux propriétés des machines virtuelles dans les **éléments répliqués**. Le panneau **Bases** affiche des informations sur les paramètres et l’état des machines.
- Pour le test de basculement, nous vous recommandons d’utiliser un réseau de machines virtuelles Azure distinct du réseau (par défaut ou personnalisé) qui a été configuré pour le basculement en production.
- Le test de basculement bascule les machines virtuelles Azure (et leur stockage) vers la région Azure secondaire. Il ne réplique aucune ressource ou application dépendante. Si les applications s’exécutant sur des machines virtuelles faisant l’objet d’un basculement sont dépendantes d’autres ressources, telles qu’Active Directory ou DNS, vous devez répliquer celles-ci également, si elles ne sont pas déjà disponibles dans votre région secondaire. [En savoir plus](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- Si vous souhaitez accéder aux machines virtuelles répliquées après le basculement à partir d’un site local, vous devez [préparer la connexion](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) à ces machines virtuelles.

## <a name="run-a-test-failover"></a>Exécution d’un test de basculement

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur l’icône **+Test de basculement** de la machine virtuelle. 

2. Dans **Test de basculement**, sélectionnez un point de récupération à utiliser pour le basculement :

    - **Dernier point traité** : bascule la machine virtuelle vers le dernier point de récupération qui a été traité par le service Site Recovery. L’horodatage est affiché. Cette option, qui n’implique aucun traitement de données, offre un objectif de délai de récupération faible.
    - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché. 
    - **Personnalisé** : sélectionnez n’importe quel point de récupération.
 
3. Sélectionnez le réseau virtuel Azure cible auquel les machines virtuelles Azure dans la région secondaire seront connectées après le basculement.
4. Pour démarrer le basculement, cliquez sur **OK**. Pour suivre la progression, cliquez sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez également cliquer sur le travail **Test de basculement** dans le nom du coffre > **Paramètres** > **Travaux** > **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Assurez-vous que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.
6. Pour supprimer les machines virtuelles qui ont été créées pendant le test de basculement, cliquez sur **Nettoyer le test de basculement** sur l’élément répliqué ou le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement. 

Pour plus d’informations sur les tests de basculement, cliquez [ici](site-recovery-test-failover-to-azure.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez testé le basculement, cette procédure pas à pas est terminée. À présent, découvrez l’exécution de basculements en production :

- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement et leur exécution.
- En savoir plus sur le basculement de plusieurs machines virtuelles [à l’aide d’un plan de récupération](site-recovery-create-recovery-plans.md).
- En savoir plus sur [l’utilisation des plans de récupération](site-recovery-create-recovery-plans.md).
- En savoir plus sur la [reprotection des machines virtuelles Azure](site-recovery-how-to-reprotect.md) après le basculement.

