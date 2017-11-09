---
title: "Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur Azure avec Site Recovery | Microsoft Docs"
description: "Découvrez comment basculer des machines virtuelles Hyper-V vers Azure et restaurer sur le site local avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur Azure

Le service [Azure Site Recovery](site-recovery-overview.md) gère et orchestre la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment basculer une machine virtuelle Hyper-V vers Azure. Une fois que vous avez procédé au basculement, vous restaurez automatiquement sur votre site local quand il est disponible. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Restaurer automatiquement des machines virtuelles Hyper-V d’un site local sur Azure
> * Basculer d’Azure vers un site local et redémarrer la réplication vers Azure

## <a name="overview"></a>Vue d'ensemble
Le basculement et la restauration automatique comportent deux étapes :

1. **Basculer vers Azure** : basculer les machines du site local vers Azure.
2. **Restaurer automatiquement depuis Azure sur le site local** : exécuter un basculement planifié depuis Azure sur un site local. Après le basculement planifié, vous activez la réplication inverse, pour redémarrer la réplication du site local vers Azure. 


## <a name="fail-over-to-azure"></a>Basculer vers Azure

### <a name="failover-prerequisites"></a>Conditions préalables de basculement

Pour effectuer un basculement :

- Veillez à effectuer un [test de récupération d’urgence](tutorial-dr-drill-azure.md) pour vérifier que tout fonctionne comme prévu.
- Vous pouvez également préparer la connexion aux machines virtuelles Azure avant de basculer.
- Vérifiez les propriétés des machines virtuelles avant d’effectuer le basculement.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Si vous voulez vous connecter à des machines virtuelles Azure à l’aide de RDP après le basculement, effectuez les opérations suivantes :

##### <a name="azure-vms-running-windows"></a>Machines virtuelles Azure exécutant Windows

1. Pour accéder à des machines virtuelles Azure via Internet, activez RDP sur la machine virtuelle locale avant le basculement. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
2. Pour accéder via un VPN de site à site, activez RDP sur la machine locale. RDP doit être autorisé dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**. Vérifiez que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [En savoir plus](https://support.microsoft.com/kb/3031135). Aucune mise à jour de Windows ne doit être en attente sur la machine virtuelle quand vous déclenchez un basculement. S’il y en a, vous ne pouvez pas vous connecter à la machine virtuelle avant la fin de la mise à jour. 
3. Sur la machine virtuelle Azure Windows, après le basculement, vérifiez les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution et lisez ces [conseils de résolution des problèmes](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>Machines virtuelles Azure exécutant Linux

1. Sur la machine locale, avant le basculement, vérifiez que le service Secure Shell est configuré pour démarrer automatiquement au démarrage du système. Vérifiez que les règles de pare-feu autorisent une connexion SSH.
2. Sur la machine virtuelle Azure, après le basculement, autorisez les connexions entrantes au port SSH pour les règles des groupes de sécurité réseau sur la machine virtuelle basculée, et pour le sous-réseau Azure auquel elle est connectée.  [Ajoutez une adresse IP publique](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) pour la machine virtuelle. Vous pouvez vérifier les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle.


#### <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Vérifiez les propriétés de la machine virtuelle et que la machine virtuelle est conforme aux [conditions requises pour Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > Machine virtuelle.
2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.
3. Dans **Calcul et réseau**, vous pouvez modifier le nom Azure, le groupe de ressources, la taille cible, le [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) et les [paramètres de disque managé](#managed-disk-considerations).
4. Vous pouvez afficher et modifier les paramètres réseau, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouvera après le basculement et l’adresse IP à lui affecter.
5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques** .


### <a name="run-a-failover-from-on-premises-to-azure"></a>Effectuer un basculement depuis le site local vers Azure

Vous pouvez effectuer un basculement régulier ou planifié pour des machines virtuelles Hyper-V.

- Utilisez un basculement régulier pour les interruptions inattendues. Quand vous effectuez ce basculement, Site Recovery crée une machine virtuelle Azure et il la démarre. Vous exécutez le basculement pour un point de récupération spécifique. Des pertes de données peuvent se produire en fonction du point de récupération que vous utilisez.
- Un basculement planifié peut être utilisé pour la maintenance ou lors d’une interruption prévue. Cette option permet de ne perdre aucune donnée. Quand un basculement planifié est déclenché, les machines virtuelles sources sont arrêtées. Les données non synchronisées sont synchronisées et le basculement est déclenché.

Cette procédure explique comment effectuer régulièrement un basculement.


1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.
2. Dans **Basculer**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
    - **Dernier** (par défaut) : cette option traite d’abord toutes les données envoyées à Site Recovery. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle Azure créée après le basculement a toutes les données qui ont été répliquées vers Site Recovery quand le basculement a été déclenché.
    - **Dernier point traité** : cette option bascule la machine virtuelle vers le dernier point de récupération traité par Site Recovery. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
    - **Dernier point de cohérence des applications** : cette option bascule la machine virtuelle vers le dernier point de récupération de cohérence des applications traité par Site Recovery. 
    - **Personnalisé** : spécifiez un point de récupération.
3. Si vous basculez des machines virtuelles Hyper-V dans des clouds System Center VMM vers Azure et que le chiffrement des données est activé pour le cloud, dans **Clé de chiffrement**, sélectionnez le certificat émis quand vous avez activé le chiffrement des données lors de l’installation du fournisseur sur le serveur VMM.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Site Recovery tente également de synchroniser les données locales qui n’ont pas encore été envoyées à Azure avant de déclencher le basculement. Notez que le basculement continue même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
5. Si vous avez préparé la connexion à la machine virtuelle Azure, connectez-vous pour la vérifier après le basculement.
6. Après vérification, **validez** le basculement. Ceci supprime tous les points de récupération disponibles.

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant que le basculement soit démarré, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.  


## <a name="fail-back-from-azure-to-on-premises"></a>Restaurer automatiquement depuis Azure vers un site local

### <a name="prerequisites-for-failback"></a>Prérequis pour la restauration automatique

Pour effectuer la restauration automatique, vérifiez que le serveur VMM/serveur Hyper-V du site principal est connecté à Site Recovery.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>Effectuer une restauration automatique et reprotéger les machines virtuelles locales

Basculez depuis Azure sur le site local et démarrez la réplication des machines virtuelles depuis le site local vers Azure.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculement planifié**.
2. Dans **Confirmer le basculement planifié**, vérifiez le sens du basculement (depuis Azure), et sélectionnez les emplacements source et cible. 
3. Dans **Synchronisation des données**, spécifiez comment vous voulez synchroniser :
    - **Synchroniser les données avant le basculement (synchroniser seulement les modifications d’ordre différentiel)** : cette option minimise le temps d’indisponibilité des machines virtuelles, car elle synchronise sans arrêter la machine virtuelle. Voici ce qu’elle fait :
        1. Elle prend une capture instantanée de la machine virtuelle Azure et la copie sur l’hôte Hyper-V local. La machine virtuelle continue de fonctionner dans Azure.
        2. Elle arrête la machine virtuelle Azure : aucune nouvelle modification ne s’y produit donc. L’ensemble final des modifications d’ordre différentiel est transféré au serveur local et la machine virtuelle locale est démarrée.
    - **Synchroniser les données uniquement lors du basculement (téléchargement complet)** : utilisez cette option si vous exécutez Azure depuis un long moment. Cette option est plus rapide, car nous attendons plusieurs modifications du disque et nous ne passons pas de temps dans les calculs des sommes de contrôle. Cette option effectue un téléchargement du disque. Elle est également pratique quand la machine virtuelle locale a été supprimée.
4. Si vous basculez des machines virtuelles Hyper-V dans des clouds System Center VMM vers Azure et que le chiffrement des données est activé pour le cloud, dans **Clé de chiffrement**, sélectionnez le certificat émis quand vous avez activé le chiffrement des données lors de l’installation du fournisseur sur le serveur VMM.
5. Lancez le basculement. Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** .
6. Si vous avez sélectionné l’option de synchronisation des données avant le basculement, quand la synchronisation initiale des données est terminée et que vous êtes prêt à arrêter les machines virtuelles Azure, cliquez sur **Tâches** > Nom de la tâche de basculement planifié > **Terminer le basculement**. Ceci arrête la machine virtuelle Azure, transfère les dernières modifications locales et démarre la machine virtuelle locale.
7. Connectez-vous à la machine virtuelle locale pour vérifier qu’elle est disponible comme prévu.
8. La machine virtuelle locale est maintenant dans l’état Validation en attente. Cliquez sur **Valider** pour valider le basculement. Ceci supprime les machines virtuelles Azure et leurs disques, et prépare la machine virtuelle locale pour la réplication inverse.
9. Pour démarrer la réplication de la machine virtuelle locale sur Azure, activez **Réplication inverse**.


> [!NOTE]
> La réplication inverse réplique seulement les modifications qui se sont produites depuis que la machine virtuelle Azure a été arrêtée, et seules les modifications différentielles sont envoyées.

