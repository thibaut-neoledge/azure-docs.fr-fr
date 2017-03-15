---
title: "Comment Site Recovery fonctionne-t-il ? | Microsoft Docs"
description: "Cet article propose une vue d’ensemble de l’architecture de Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 22b50dd6242e8c10241b0626b48f8ef842b6b0fd
ms.openlocfilehash: c33ca9e5292096a0fd96d98da3e89d721463e903
ms.lasthandoff: 03/02/2017


---
# <a name="how-does-azure-site-recovery-work"></a>Comment Azure Site Recovery fonctionne-t-il ?

Lisez cet article pour comprendre l’architecture sous-jacente du service [Azure Site Recovery](site-recovery-overview.md) et les composants qui le font fonctionner.

Publiez des commentaires au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="replication-to-azure"></a>Réplication sur Azure

Vous pouvez répliquer les éléments suivants dans Azure :
- **VMware**: Machines virtuelles VMware locales s’exécutant sur un [hôte pris en charge](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Vous pouvez répliquer des machines virtuelles VMware exécutant les [systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
- **Hyper-V**: machines virtuelles Hyper-V locales s’exécutant sur des [hôtes pris en charge](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- **Machines physiques**: serveurs physiques locaux exécutant Windows ou Linux sur les [systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Vous pouvez répliquer des machines virtuelles Hyper-V exécutant un système d’exploitation invité [pris en charge par Hyper-V et Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="vmware-replication-to-azure"></a>Réplication VMware vers Azure

Domaine | Composant | Détails
--- | --- | ---
**Microsoft Azure** | Dans Azure, vous avez besoin d’un Compte Azure, d’un compte de stockage Azure et d’un Réseau Azure. | Le stockage et le réseau peuvent être des comptes Resource Manager ou Classic.<br/><br/>  Les données répliquées sont stockées dans le compte de stockage, et les machines virtuelles Azure sont créées avec les données répliquées lors du basculement à partir de votre site local. Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Serveur de configuration** | Un seul serveur d’administration (Machine virtuelle VMware) exécute tous les composants locaux - serveur de configuration, serveur de traitement, serveur cible maître | Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
 **Serveur de traitement**:  | Installé par défaut sur le serveur de configuration. | Fait office de passerelle de réplication. Reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.<br/><br/> Le serveur de traitement gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware.<br/><br/> À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement dédiés supplémentaires afin de gérer de plus grands volumes de trafic de réplication.
 **Serveur cible maître** | Installé par défaut sur le serveur de configuration local. | Gère les données de réplication pendant la restauration automatique à partir d’Azure.<br/><br/> Si les volumes de trafic de restauration automatique sont importants, vous pouvez déployer un serveur cible maître distinct pour la restauration.
**Serveurs VMware** | Les machines virtuelles VMware sont hébergées sur des serveurs vSphere ESXi et nous vous recommandons d’utiliser un serveur vCenter pour gérer les ordinateurs hôtes. | Vous ajoutez des serveurs VMware à votre coffre Recovery Services.<br/><br/> I
**Machines répliquées** | Le service Mobilité sera installé sur chaque machine virtuelle VMware que vous souhaitez répliquer. Il peut être installé manuellement sur chaque ordinateur ou avec une installation push à partir du serveur de traitement.

**Figure 1 : composants VMware vers Azure**

![Composants](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Processus de réplication

1. Vous configurez le déploiement, y compris les composants Azure et un coffre Recovery Services. Dans le coffre, vous spécifiez la source et la cible de réplication, configurez le serveur de configuration, ajoutez des serveurs VMware, créez une stratégie de réplication, déployez le service Mobility, autorisez la réplication et effectuez un test de basculement.
2.  Les machines débutent la réplication conformément à la stratégie définie, et une copie initiale des données est répliquée sur le stockage Azure.
4. La réplication des modifications delta dans Azure commence une fois la réplication initiale terminée. Le suivi des modifications d’une machine est conservé dans un fichier .hrl.
    - Les machines qui assurent la réplication communiquent avec le serveur de configuration sur le port HTTPS 443 entrants, pour la gestion de la réplication.
    - Les machines qui assurent la réplication envoient des données de réplication au serveur de traitement sur le port HTTPS 9443 entrant (configuration possible).
    - Le serveur de configuration orchestre la gestion de la réplication avec Azure sur le port HTTPS 443 sortant.
    - Le serveur de traitement reçoit les données à partir des machines source, puis les chiffre et les envoie au stockage Azure via le port 443 sortant.
    - Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004. Le mode multimachine virtuelle est utilisé si vous regroupez plusieurs machines dans des groupes de réplication qui partagent des points de récupération cohérents en cas d’incident et avec les applications lorsqu’ils basculent. Cela est utile si les machines exécutent la même charge de travail et doivent être cohérentes.
5. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet. L’autre solution consiste à utiliser l’[homologation publique](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering) Azure ExpressRoute. La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.

**Figure 2 : réplication de VMware vers Azure**

![Amélioré](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback"></a>Basculement et restauration automatique

1. Après avoir vérifié que le test de basculement fonctionne comme prévu, vous pouvez exécuter des basculements non planifiés sur Azure en fonction des besoins. Le basculement planifié n’est pas pris en charge.
2. Vous pouvez basculer une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour basculer plusieurs machines virtuelles.
3. Lorsque vous effectuez un basculement, les machines virtuelles répliquées sont créées dans Azure. Vous validez un basculement pour accéder à la charge de travail à partir de la machine virtuelle Azure répliquée.
4. Lorsque votre site local principal est à nouveau disponible, vous pouvez lancer la restauration automatique. Vous configurez une infrastructure de restauration automatique, lancez la réplication de la machine à partir du site secondaire vers le site principal et effectuez un basculement non planifié à partir du site secondaire. Une fois ce basculement validé, les données sont à nouveau disponibles en local et vous devez relancer la réplication sur Azure. [En savoir plus](site-recovery-failback-azure-to-vmware.md)

La restauration automatique doit répondre à plusieurs conditions requises :


- **Serveur de traitement temporaire dans Azure**: si vous souhaitez effectuer une restauration automatique à partir d’Azure après le basculement, vous devez définir une machine virtuelle Azure configurée comme serveur de traitement pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.
- **Connexion VPN**: pour une restauration automatique, vous devrez disposer d’une connexion VPN (ou d’Azure ExpressRoute) configurée à partir du réseau Azure sur le site local.
- **Serveur cible maître local distinct** : le serveur cible maître local gère la restauration automatique. Le serveur cible maître est installé par défaut sur le serveur d’administration. Toutefois, si vous restaurez automatiquement de plus grands volumes de trafic vous devez définir un serveur cible maître local à cet effet.
- **Stratégie de restauration automatique** : pour répliquer vers votre site local, vous avez besoin d’une stratégie de restauration automatique. Celle-ci est automatiquement créée lors de la création de votre stratégie de réplication.

**Figure 3 : restauration automatique VMware/physique**

![Restauration automatique](./media/site-recovery-components/enhanced-failback.png)

## <a name="physical-server-replication-to-azure"></a>Réplication de serveur physique sur Azure

Ce scénario de réplication utilise également les mêmes composants et processus en tant que [VMware vers Azure](#vmware-replication-to-azure), mais notez ces différences :

- Vous pouvez utiliser un serveur physique pour le serveur de configuration, au lieu d’une machine virtuelle VMware
- Il vous faudra une infrastructure VMware locale pour la restauration. Il est impossible d’effectuer une restauration sur un serveur physique.

## <a name="hyper-v-replication-to-azure"></a>Réplication Hyper-V vers Azure

**Zone** | **Composant** | **Détails**
--- | --- | ---
**Microsoft Azure** | Dans Azure, vous avez besoin d’un compte Microsoft Azure, d’un compte de stockage Azure et d’un réseau Azure. | Le stockage et le réseau peuvent être des comptes Resource Manager ou Classic.<br/><br/> Les données répliquées sont stockées dans le compte de stockage, et les machines virtuelles Azure sont créées avec les données répliquées lors du basculement à partir de votre site local.<br/><br/> Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Serveur VMM** | Hôtes Hyper-V situés dans des clouds VMM | Si les hôtes Hyper-V sont gérés dans des clouds VMM, vous inscrivez le serveur VMM dans le coffre Recovery Services.<br/><br/> Sur le serveur VMM, vous installez le fournisseur Site Recovery pour orchestrer la réplication avec Azure.<br/><br/> Vous avez besoin d’un réseau logique et d’un réseau de machines virtuelles pour configurer le mappage réseau. Un réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud.
**Hôte Hyper-V** | Les serveurs Hyper-V peuvent être déployés avec ou sans serveur VMM. | S’il n’existe aucun serveur VMM, le fournisseur Site Recovery est installé sur l’hôte pour orchestrer la réplication avec Site Recovery via Internet. S’il existe un serveur VMM, le fournisseur est installé sur ce dernier et non sur l’hôte.<br/><br/> L’agent Recovery Services est installé sur l’hôte pour gérer la réplication des données.<br/><br/> Les communications en provenance du fournisseur et de l’agent sont sécurisées et chiffrées. Les données répliquées dans le stockage Azure sont également chiffrées.
**Machines virtuelles Hyper-V** | Vous devez avoir une ou plusieurs machines virtuelles sur le serveur hôte Hyper-V. | Rien ne doit être installé de manière explicite sur des machines virtuelles


### <a name="replication-process"></a>Processus de réplication

1. Vous configurez les composants Azure. Nous vous recommandons de configurer les comptes de stockage et de réseau avant de lancer le déploiement de Site Recovery.
2. Vous créez un coffre Replication Services pour Site Recovery et configurez les paramètres du coffre, notamment :
    - Paramètres source et cible. Si vous ne gérez pas les hôtes Hyper-V dans un cloud VMM, pour la cible vous créez un conteneur de site Hyper-V et y ajoutez des hôtes Hyper-V. Si les hôtes Hyper-V sont gérés dans VMM, la source est le cloud VMM. La cible est Azure.
    - Installation du fournisseur Azure Site Recovery et de l’agent Microsoft Azure Recovery Services. Si vous avez VMM, le fournisseur sera installé dessus et l’agent sur chaque hôte Hyper-V. Si vous n’avez pas VMM, le fournisseur et l’agent sont installés sur chaque hôte.
    - Vous créez une stratégie de réplication pour le site Hyper-V ou un cloud VMM. La stratégie est appliquée à toutes les machines virtuelles situées sur les hôtes du site ou du cloud.
    - Vous activez la réplication pour les machines virtuelles Hyper-V. La réplication initiale se déclenche selon les paramètres de la stratégie de réplication.
4. Les modifications des données font l’objet d’un suivi et la réplication des modifications delta dans Azure débute après la réplication initiale. Les modifications qui font l’objet d’un suivi sont conservées dans un fichier .hrl.
5. Vous effectuez un test de basculement pour vérifier que tout fonctionne bien.

### <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

1. Vous pouvez effectuer un [basculement](site-recovery-failover.md) planifié ou non planifié vers Azure à partir de machines virtuelles Hyper-V locales. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.
2. Vous pouvez basculer sur une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour orchestrer le basculement de plusieurs machines.
4. Une fois le basculement effectué, vous pouvez voir les machines virtuelles répliquées dans Azure. Si nécessaire, vous pouvez affecter une adresse IP publique à la machine virtuelle.
5. Vous validez ensuite le basculement pour accéder à la charge de travail à partir de la machine virtuelle Azure répliquée.
6. Lorsque votre site local principal est à nouveau disponible, vous pouvez lancer la [restauration automatique](site-recovery-failback-from-azure-to-hyper-v.md). Vous effectuez un basculement planifié depuis Azure vers le site principal. Pour un basculement planifié, vous pouvez choisir d’effectuer une restauration automatique vers la même machine virtuelle ou un autre emplacement et synchroniser les modifications entre Azure et un emplacement local pour éviter toute perte de données. Lorsque les machines virtuelles sont créées en local, vous validez le basculement.

**Figure 4 : réplication de site Hyper-V vers Azure**

![Composants](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figure 5 : réplication de site Hyper-V dans des clouds VMM vers Azure**

![Composants](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-to-a-secondary-site"></a>Réplication vers un site secondaire

Vous pouvez répliquer les éléments suivants vers votre site secondaire :

- **VMware**: Machines virtuelles VMware locales s’exécutant sur un [hôte pris en charge](site-recovery-support-matrix-to-sec-site.md#on-premises-servers). Vous pouvez répliquer des machines virtuelles VMware exécutant les [systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)
- **Machines physiques**: serveurs physiques locaux exécutant Windows ou Linux sur les [systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: machines virtuelles Hyper-V locales s’exécutant sur des [hôtes Hyper-V pris en charge](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) gérés dans des clouds VMM. [Systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Vous pouvez répliquer des machines virtuelles Hyper-V exécutant un système d’exploitation invité [pris en charge par Hyper-V et Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="vmware-vmphysical-server-replication-to-a-secondary-site"></a>Réplication de machines virtuelles VMware/serveurs physiques vers un site secondaire

### <a name="components"></a>Composants

**Zone** | **Composant** | **Détails**
--- | --- | ---
**Microsoft Azure** | Vous déployez ce scénario à l’aide d’InMage Scout. | Pour obtenir InMage Scout, vous avez besoin d’un abonnement Azure.<br/><br/> Après avoir créé un coffre Recovery Services, téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement.
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

**Figure 6 : réplication de VMware vers VMware**

![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="hyper-v-vm-replication-to-a-secondary-site"></a>Réplication machine virtuelle Hyper-V vers un site secondaire


**Zone** | **Composant** | **Détails**
--- | --- | ---
**Microsoft Azure** | Vous devez avoir un compte Microsoft Azure. |
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

**Figure 7 : réplication de VMM vers VMM**

![Local vers local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback"></a>Basculement et restauration automatique

1. Vous pouvez effectuer un [basculement](site-recovery-failover.md) planifié ou non planifié entre des sites locaux. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.
2. Vous pouvez basculer vers une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour orchestrer le basculement de plusieurs machines.
4. Si vous effectuez un basculement non planifié vers un site secondaire, une fois l’opération terminée, les machines de l’emplacement secondaire ne sont pas protégées ou réplicables. Si vous avez lancé un basculement planifié, une fois l’opération terminée, les machines de l’emplacement secondaire sont protégées.
5. Ensuite, validez le basculement pour accéder à la charge de travail à partir de la machine virtuelle répliquée.
6. Lorsque votre site principal est à nouveau disponible, vous déclenchez la réplication inverse depuis le site secondaire vers le site principal. La réplication inverse affecte aux machines virtuelles l’état protégé, mais l’emplacement actif est toujours le centre de données secondaire.
7. Pour placer le site principal à l’emplacement actif, lancez un basculement planifié depuis le site secondaire vers le site principal, puis une autre réplication inverse.


## <a name="hyper-v-replication-workflow"></a>Flux de travail de réplication Hyper-V

**Étape du flux de travail** | **Action**
--- | ---
1. **Activer la protection** | Lorsque vous activez la protection d’une machine virtuelle Hyper-V, le travail **Activer la protection** est lancé pour vérifier que la machine répond aux conditions requises. Le travail appelle deux méthodes :<br/><br/> [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) pour configurer la réplication avec les paramètres que vous avez configurés.<br/><br/> [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication de machine virtuelle complète.
2. **Réplication initiale** |  Un instantané de la machine virtuelle est créé et les disques durs virtuels sont répliqués un par un jusqu’à ce qu’ils soient tous copiés dans l’emplacement secondaire.<br/><br/> La durée de cette opération dépend de la taille de la machine virtuelle, de la bande passante réseau et de la méthode de réplication initiale que vous avez choisie.<br/><br/> Si des modifications interviennent sur les disques pendant la réplication initiale, le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications dans des journaux de réplication Hyper-V (.hrl), qui se trouvent dans le même dossier que les disques.<br/><br/> À chaque disque correspond un fichier .hrl, qui est envoyé au stockage secondaire.<br/><br/> L’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale. À la fin de la réplication initiale, l’instantané de machine virtuelle est supprimé et les modifications de disque delta figurant dans le journal sont synchronisées et fusionnées.
3. **Finaliser la protection** | Une fois la réplication initiale terminée, le travail **Finaliser la protection** configure les paramètres réseau et d’autres paramètres de post-réplication, pour protéger la machine virtuelle.<br/><br/> Si vous répliquez dans Azure, vous devrez peut-être modifier les paramètres de la machine virtuelle pour la préparer au basculement.<br/><br/> À ce stade, vous pouvez exécuter un basculement de test pour vérifier que tout fonctionne comme prévu.
4. **Réplication** | La synchronisation delta commence à l’issue de la réplication initiale, selon les paramètres de réplication.<br/><br/> **Échec de la réplication**: si la réplication différentielle échoue et si une réplication complète serait coûteuse en bande passante et en temps, une resynchronisation se produit. Par exemple, si les fichiers .hrl atteignent 50 % de la taille du disque, la machine virtuelle est marquée pour resynchronisation. La resynchronisation réduit la quantité de données envoyées par le calcul des sommes de contrôle des machines virtuelles source et cible et l’envoi du seul delta. Une fois la resynchronisation terminée, la réplication différentielle doit reprendre. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement.<br/><br/> **Erreur de réplication** : si une erreur de réplication se produit, une nouvelle tentative intégrée est effectuée. S’il s’agit d’une erreur non récupérable, telle qu’une erreur d’authentification ou d’autorisation ou qu’une machine de réplication est dans un état non valide, aucune nouvelle tentative n’intervient. S’il s’agit d’une erreur récupérable, par exemple une erreur réseau ou un manque d’espace disque ou de mémoire, une nouvelle tentative se produit à intervalles croissants (après 1, 2, 4, 8 et 10 minutes, puis toutes les 30 minutes).
5. **Basculement planifié/non planifié** | Vous pouvez effectuer des basculements planifiés ou non planifiés si nécessaire.<br/><br/> Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.<br/><br/> Une fois créées, les machines virtuelles de réplication sont en attente de validation. Vous devez les valider pour finaliser le basculement.<br/><br/> Une fois le site principal opérationnel, vous pouvez restaurer automatiquement le site principal, lorsqu’il est disponible.


**Figure 8 : flux de travail Hyper-V**

![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)




## <a name="next-steps"></a>Étapes suivantes

[Vérifiez les composants requis](site-recovery-prereq.md)

