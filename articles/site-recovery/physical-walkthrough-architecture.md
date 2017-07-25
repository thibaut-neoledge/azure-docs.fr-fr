---
title: "Vérifier l’architecture de la réplication de serveurs physiques dans Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de serveurs physiques locaux dans Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 09c9b136-35f5-465e-8d0f-f4c5d5d9f880
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 843c3f1b54f50fe50b162ed242deab717a080830
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---

# <a name="step-1-review-the-architecture-for-physical-server-replication-to-azure"></a>Étape 1 : vérifier l’architecture de la réplication de serveurs physiques dans Azure

Cet article décrit les composants et les processus utilisés lorsque vous répliquez des serveurs physiques Windows/Linux locaux dans Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Composants architecturaux

Le tableau récapitule les composants dont vous avez besoin.

**Composant** | **Prérequis** | **Détails**
--- | --- | ---
**Microsoft Azure** | Vous avez besoin d’un compte Azure, d’un compte de stockage Azure et d’un réseau Azure. | Les données répliquées sont stockées dans le compte de stockage, et les machines virtuelles Azure sont créées avec les données répliquées lors du basculement. Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Serveur de configuration** | Un seul serveur d’administration local (serveur physique ou machine virtuelle VMware) qui exécute tous les composants locaux de Site Recovery. Ces derniers incluent un serveur de configuration, un serveur de processus et un serveur cible maître. | Le composant de serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
 **Serveur de traitement**  | Installé par défaut sur le serveur de configuration. | Fait office de passerelle de réplication. Reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.<br/><br/> Le serveur de processus gère également l’installation Push du service Mobilité sur des machines protégées.<br/><br/> Vous pouvez ajouter séparément des serveurs de processus dédiés supplémentaires afin de gérer de plus grands volumes de trafic de réplication.
 **Serveur cible maître** | Installé par défaut sur le serveur de configuration local. | Gère les données de réplication pendant la restauration automatique à partir d’Azure.<br/><br/> Si les volumes de trafic de restauration automatique sont importants, vous pouvez déployer un serveur cible maître distinct pour la restauration.
**Serveurs répliqués** | Le composant service Mobilité est installé sur chaque serveur Windows/Linux à répliquer. Il peut être installé manuellement sur chaque ordinateur ou avec une installation push à partir du serveur de traitement.
**Restauration automatique** | Une infrastructure VMware est requise pour la restauration automatique. Il est impossible d’effectuer cette procédure vers un serveur physique.


**Figure 1 : serveur physique vers composants Azure**

![Composants](./media/physical-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processus de réplication

1. Vous configurez le déploiement, notamment celui des composants locaux et des composants Azure. Dans le coffre Recovery Services, vous spécifiez la source et la cible de réplication, configurez le serveur de configuration, créez une stratégie de réplication, déployez le service Mobilité, activez la réplication et effectuez un test de basculement.
2.  Les machines répliquent conformément à la stratégie définie, et une copie initiale des données est répliquée dans le stockage Azure.
4. La réplication des modifications delta dans Azure commence à l’issue de la réplication initiale. Le suivi des modifications d’une machine est conservé dans un fichier .hrl.
    - Les machines qui assurent la réplication communiquent avec le serveur de configuration sur le port HTTPS 443 entrants, pour la gestion de la réplication.
    - Les machines qui assurent la réplication envoient des données de réplication au serveur de processus sur le port HTTPS 9443 entrant (modification possible).
    - Le serveur de configuration orchestre la gestion de la réplication avec Azure sur le port HTTPS 443 sortant.
    - Le serveur de traitement reçoit les données à partir des machines source, puis les chiffre et les envoie au stockage Azure via le port 443 sortant.
    - Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004. Le mode multimachine virtuelle est utilisé si vous regroupez plusieurs machines dans des groupes de réplication qui partagent des points de récupération cohérents en cas d’incident et avec les applications lorsqu’ils basculent. Cela est utile si les machines exécutent la même charge de travail et doivent être cohérentes.
5. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet. L’autre solution consiste à utiliser l’[homologation publique](../expressroute/expressroute-circuit-peerings.md#public-peering) Azure ExpressRoute. La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.

**Figure 2 : réplication de serveur physique dans Azure**

![Amélioré](./media/physical-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

Après avoir vérifié que le test de basculement fonctionne comme prévu, vous pouvez exécuter des basculements non planifiés sur Azure en fonction des besoins. Lorsque vous exécutez un basculement, les machines virtuelles Azure sont créées à partir des données répliquées. Lorsque votre site local principal est à nouveau disponible, vous pouvez lancer la restauration automatique. Notez les points suivants :

- Le basculement planifié n’est pas pris en charge.
- Vous pouvez basculer une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour basculer ensemble plusieurs machines virtuelles.
- Vous validez un basculement pour accéder à la charge de travail à partir de la machine virtuelle Azure répliquée.
- Lorsque le site principal est à nouveau disponible, vous répliquez la machine du site secondaire vers le site principal. Exécutez ensuite un basculement non planifié à partir du site secondaire. Une fois ce basculement validé, les données sont à nouveau disponibles en local et vous devez relancer la réplication sur Azure.

Composants de la restauration automatique :

- **Serveur de processus temporaire dans Azure** : vous devez configurer une machine virtuelle Azure en tant que serveur de processus pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.
- **Connexion VPN** : vous avez besoin d’une connexion VPN (ou Azure ExpressRoute) du réseau Azure vers le site local.
- **Serveur cible maître local distinct** : le serveur cible maître local (installé par défaut sur le serveur de configuration) gère la restauration automatique. Si vous restaurez automatiquement de grands volumes de trafic, vous devez configurer un serveur cible maître local distinct à cet effet.
- **Stratégie de restauration automatique** : vous devez définir une stratégie de restauration automatique. Celle-ci est automatiquement créée lors de la création de votre stratégie de réplication.
- **Infrastructure VMware** : vous devez basculer vers une machine virtuelle VMware locale. Cela signifie que vous avez besoin d’une infrastructure VMware locale, même si vous répliquez des serveurs physiques locaux vers Azure.

**Figure 3 : restauration automatique de serveur physique**

![Restauration automatique](./media/physical-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Étapes suivantes

Accédez à [Étape 2 : vérifier les conditions préalables et les limitations](physical-walkthrough-prerequisites.md).

