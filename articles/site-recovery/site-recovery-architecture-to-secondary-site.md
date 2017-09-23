---
title: "Comment fonctionne la réplication de machines locales vers un site local secondaire dans Azure Site Recovery ? | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de serveurs physiques et de machines virtuelles locaux vers un site secondaire avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017

---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Comment fonctionne la réplication de machines locales vers un site secondaire dans Site Recovery ?

Cet article décrit les composants et les processus impliqués dans la réplication des serveurs physiques et des machines virtuelles locaux sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Vous pouvez répliquer les éléments suivants vers un site local secondaire :
- Machines virtuelles Hyper-V locales, sur clusters Hyper-V et hôtes autonomes qui sont gérés dans des clouds System Center Virtual Machine Manager (VMM).
- Machines virtuelles VMware locales et serveurs physiques Windows/Linux. Dans ce scénario, la réplication est gérée par Scout.

Publiez des commentaires au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Répliquer des machines virtuelles Hyper-V vers un site local secondaire


### <a name="architectural-components"></a>Composants architecturaux

Voici ce dont vous avez besoin pour la réplication des machines virtuelles Hyper-V vers un site secondaire.

**Composant** | **Emplacement** | **Détails**
--- | --- | ---
**Microsoft Azure** | Vous avez besoin d’un compte Microsoft. |
**Serveur VMM** | Nous recommandons l’utilisation d’un serveur VMM dans le site principal et un autre dans le site secondaire | Chaque serveur VMM doit être connecté à Internet.<br/><br/> Chaque serveur doit avoir au moins un cloud privé VMM avec l’ensemble des profils de fonctionnalités Hyper-V.<br/><br/> Vous installez le fournisseur Azure Site Recovery sur le serveur VMM. Le fournisseur coordonne et orchestre la réplication avec le service Site Recovery via Internet. Les communications entre le fournisseur et Azure sont sécurisées et chiffrées.
**Serveur Hyper-V** |  Un ou plusieurs serveurs hôtes Hyper-V dans les clouds VMM principaux et secondaires.<br/><br/> Les serveurs doivent être connectés à Internet.<br/><br/> Les données sont répliquées entre les serveurs hôtes Hyper-V principal et secondaire via une liaison LAN ou VPN, en utilisant Kerberos ou une authentification par certificat.  
**Machines virtuelles Hyper-V** | Situé sur le serveur hôte Hyper-V source. | Le serveur hôte source doit avoir au moins une machine virtuelle que vous souhaitez répliquer.

### <a name="replication-process"></a>Processus de réplication

1. Vous configurez le compte Azure.
2. Vous créez un coffre Replication Services pour Site Recovery et configurez les paramètres du coffre, notamment :

    - Source et cible de la réplication (sites principal et secondaire).
    - Installation du fournisseur Azure Site Recovery et de l’agent Microsoft Azure Recovery Services. Le fournisseur est installé sur les serveurs VMM et l’agent sur chaque hôte Hyper-V.
    - Vous créez une stratégie de réplication pour le cloud VMM source. La stratégie est appliquée à toutes les machines virtuelles situées sur des hôtes dans le cloud.
    - Vous activez la réplication pour les machines virtuelles Hyper-V. La réplication initiale se déclenche selon les paramètres de la stratégie de réplication.
4. Les modifications des données font l’objet d’un suivi et la réplication des modifications delta débute après la réplication initiale. Les modifications qui font l’objet d’un suivi sont conservées dans un fichier .hrl.
5. Vous effectuez un test de basculement pour vérifier que tout fonctionne bien.

**Figure 1 : réplication de VMM vers VMM**

![Local vers local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

1. Vous pouvez effectuer un [basculement](site-recovery-failover.md) planifié ou non planifié entre des sites locaux. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.
2. Vous pouvez basculer vers une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour orchestrer le basculement de plusieurs machines.
4. Si vous effectuez un basculement non planifié vers un site secondaire, une fois l’opération terminée, les machines de l’emplacement secondaire ne sont pas protégées ou réplicables. Si vous avez lancé un basculement planifié, une fois l’opération terminée, les machines de l’emplacement secondaire sont protégées.
5. Ensuite, validez le basculement pour accéder à la charge de travail à partir de la machine virtuelle répliquée.
6. Lorsque votre site principal est à nouveau disponible, vous déclenchez la réplication inverse depuis le site secondaire vers le site principal. La réplication inverse affecte aux machines virtuelles l’état protégé, mais l’emplacement actif est toujours le centre de données secondaire.
7. Pour placer le site principal à l’emplacement actif, lancez un basculement planifié depuis le site secondaire vers le site principal, puis une autre réplication inverse.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Réplication de machines virtuelles VMware/serveurs physiques sur un site secondaire

Vous répliquez les machines virtuelles ou les serveurs physiques VMware vers un site secondaire à l’aide d’InMage Scout, à l’aide des composants architecturaux suivants :


### <a name="architectural-components"></a>Composants architecturaux

**Composant** | **Emplacement** | **Détails**
--- | --- | ---
**Microsoft Azure** | InMage Scout. | Pour obtenir InMage Scout, vous avez besoin d’un abonnement Azure.<br/><br/> Après avoir créé un coffre Recovery Services, téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement.
**Serveur de traitement** | Situé dans le site principal | Vous déployez le serveur de traitement pour gérer la mise en cache, la compression et l’optimisation des données.<br/><br/> Il gère aussi l’installation Push de l’Agent unifié sur les machines à protéger.
**Serveur de configuration** | Situé sur le site secondaire | Le serveur de configuration gère, configure et surveille votre déploiement via le site web de gestion ou la console vContinuum.
**Serveur vContinuum** | facultatif. Installé au même emplacement que le serveur de configuration. | Il intègre une console qui vous permet de gérer et surveiller votre environnement protégé.
**Serveur cible maître** | Situé sur le site secondaire | Le serveur cible maître stocke les données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplication sur le site secondaire et stocke les points de rétention des données.<br/><br/> Le nombre de serveurs cibles maîtres dont vous avez besoin dépend du nombre de machines que vous protégez.<br/><br/> Si vous voulez effectuer une restauration sur le site principal, vous avez là aussi besoin d’un serveur cible maître. L’Agent unifié est installé sur ce serveur.
**Serveur VMware ESX/ESXi et vCenter** |  Les machines virtuelles sont hébergées sur des hôtes ESX/ESXi. Les hôtes sont gérés avec un serveur vCenter | Vous avez besoin d’une infrastructure VMware pour répliquer des machines virtuelles VMware.
**Machines virtuelles/serveurs physiques** |  L’Agent unifié installé sur les machines virtuelles VMware et les serveurs physiques que vous souhaitez répliquer. | Il joue le rôle de fournisseur de communication entre tous les composants.


### <a name="replication-process"></a>Processus de réplication

1. Vous configurez les serveurs de composants dans chaque site (configuration, processus, cible maître) et installez l’Agent unifié sur les ordinateurs que vous souhaitez répliquer.
2. Après la réplication initiale, l’agent de chaque machine envoie les modifications de réplication différentielle au serveur de traitement.
3. Le serveur de traitement optimise ces données et les transfère vers le serveur cible maître du site secondaire. Le serveur de configuration gère le processus de réplication.

**Figure 2 : réplication de VMware vers VMware**

![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Étapes suivantes

Examen de la [matrice de prise en charge](site-recovery-support-matrix-to-sec-site.md)

