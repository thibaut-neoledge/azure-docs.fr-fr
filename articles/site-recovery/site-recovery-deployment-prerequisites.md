---
title: "Conditions préalables au déploiement d’Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit les conditions préalables pour la configuration de la réplication avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Conditions préalables au déploiement de Site Recovery

Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Cet article résume les conditions préalables au déploiement pour les scénarios de réplication Site Recovery.  

Envoyez vos commentaires au bas de cet article ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modèles de déploiement Azure

Azure dispose de deux [modèles de déploiement](../azure-resource-manager/resource-manager-deployment-model.md) pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle classique. De plus, Azure propose deux portails : le [portail Azure Classic](https://manage.windowsazure.com/), qui prend en charge le modèle de déploiement classique, et le [portail Azure](https://ms.portal.azure.com/), qui gère les deux modèles de déploiement.

Des nouveaux scénarios Site Recovery doivent être déployés dans le [portail Azure](https://portal.azure.com). Ce portail fournit de nouvelles fonctionnalités et une expérience de déploiement optimisée. Des coffres peuvent être gérés dans le portail classique, mais vous ne pouvez pas en créer de nouveaux.


## <a name="azure-account-requirements"></a>Conditions requises pour le compte Azure

**Prérequis** | **Détails**
--- | --- 
**Compte Azure** | Un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.


## <a name="azure-storage-requirements"></a>Conditions requises pour le stockage Azure

Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement.

**Prérequis** | **Détails**
--- | --- 
[Compte Stockage Azure](../storage/storage-introduction.md) | Vous pouvez utiliser le stockage [GRS](../storage/storage-redundancy.md#geo-redundant-storage) ou LRS.<br/><br/> Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée. [En savoir plus](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Portail Azure** | Dans le portail Azure, vous pouvez utiliser un compte de stockage Resource Manager ou un compte classique.
**Stockage Premium** | Le [stockage Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) est pris en charge si vous répliquez des machines virtuelles VMware ou des serveurs physiques dans Azure, à l’aide du portail Azure.<br/><br/> Le stockage Premium est généralement utilisé pour les machines virtuelles nécessitant des performances d’E/S élevées et une faible latence pour héberger les charges de travail nécessitant beaucoup d’E/S.<br/><br/> Si vous utilisez un stockage Premium, vous avez également besoin d’un compte de stockage standard pour stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.
**Limites de stockage** | Le portail classique ne prenait en charge que GRS.<br/><br/> Les comptes de stockage créés dans le Portail Azure ne peuvent pas passer d’un groupe de ressources à l’autre.<br/><br/> La réplication des comptes de stockage Premium au Centre de l'Inde et dans l’Inde du Sud n’est actuellement pas prise en charge.

## <a name="azure-network-requirements"></a>Conditions requises pour le réseau Azure

Vous devez avoir un réseau Azure, auquel les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement.

**Prérequis** | **Détails**
--- | ---
**Région du réseau** | Ce réseau doit se trouver dans la même région que le coffre.
**Portail Azure** | Dans le portail Azure, vous pouvez utiliser un réseau Resource Manager ou un réseau classique.
**Mappage réseau** | Si vous répliquez à partir de VMM vers Azure, vous allez configurer [le mappage réseau](site-recovery-network-mapping.md) entre les réseaux VMM VM et Azure pour vous assurer que les machines virtuelles Azure se connectent aux réseaux appropriés après le basculement.


## <a name="vmware-replication-requirements-to-azure"></a>Conditions requises pour la réplication VMware (vers Azure)

**Composant** | **Prérequis**
--- | ---
**Serveur de configuration** | Un serveur physique ou une machine virtuelle en local, qui exécute Windows Server 2012 R2. Tous les composants Site Recovery locaux sont installés sur cette machine.<br/><br/>Pour la réplication de machine virtuelle VMware, nous vous conseillons de déployer le serveur en tant que machine virtuelle VMware hautement disponible. <br/><br/>Si le serveur est une machine virtuelle VMware, la carte réseau doit être de type VMXNET3. Si vous utilisez un autre type de carte réseau, installez une [mise à jour de VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) sur le serveur vSphere 5.5.<br/><br/>Le serveur doit avoir une adresse IP statique.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le nom d’hôte du serveur doit comprendre 15 caractères ou moins.<br/><br/>Le système d’exploitation doit être en anglais uniquement.<br/><br/> Installez VMware vSphere PowerCLI 6.0. serveur.<br/><br/>Le serveur de configuration nécessite un accès à Internet. L’accès sortant suivant est requis :<br/><br/>Accès temporaire sur HTTP 80 lors de l’installation des composants de Site Recovery (pour télécharger MySQL)<br/><br/>Accès sortant permanent sur HTTPS 443 pour la gestion de la réplication<br/><br/>Accès sortant permanent sur HTTPS 9443 pour le trafic de réplication (ce port peut être modifié)<br/><br/> Autorisez cette URL pour le téléchargement de MySQL :``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> Le serveur doit également accéder à [ces URL](#requirements-for-url-access)
**Serveur de traitement** | Installé par défaut sur le serveur de configuration<br/><br/> Fait office de passerelle de réplication. Il reçoit les données de réplication d’ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement et envoie les données vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement dédiés supplémentaires afin de gérer de plus grands volumes de trafic de réplication.
**Serveur cible maître** | Installé par défaut sur le serveur de configuration. Gère les données de réplication pendant la restauration automatique à partir d’Azure.
**Hôtes vSphere** | Un ou de plusieurs hyperviseurs VMware vSphere.<br/><br/>Les hyperviseurs doivent exécuter vSphere version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/>Nous vous recommandons de placer les hôtes vSphere et les serveurs vCenter sur le même réseau que le serveur de processus (le réseau dans lequel se trouve le serveur de configuration, sauf si vous avez configuré un serveur de processus dédié). 
**Serveurs vCenter** | Nous vous recommandons de déployer un serveur VMware vCenter pour gérer vos hôtes vSphere. Ce dernier doit exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>**Limitation** : Site Recovery ne prend pas en charge les nouvelles fonctionnalités de vCenter et vSphere 6.0, telles que Cross vCenter vMotion, les volumes virtuels et Storage DRS. La prise en charge de Site Recovery est limitée aux fonctionnalités qui étaient déjà disponibles dans la version 5.5

### <a name="vmware-vm-requirements-to-azure"></a>Conditions requises pour la machine virtuelle VMware (vers Azure)

**Composant** | **Prérequis**
--- | ---
**Machines virtuelles VMware** | Les outils VMware doivent être installés sur les machines virtuelles répliquées, et elles doivent être en cours d’exécution.<br/><br/> Les machines virtuelles doivent répondre à la [configuration requise Azure](site-recovery-support-matrix.md#support-for-azure-vms) pour la création de machines virtuelles Azure.<br/><br/>Sur les machines protégées, la capacité d’un disque ne doit pas dépasser 1023 Go. Une machine virtuelle peut comporter jusqu’à 64 disques (jusqu’à 64 To). <br/><br/>2 Go minimum d’espace disponible sur le lecteur d’installation pour l’installation du composant.<br/><br/>**Limitation** : la protection des machines virtuelles avec disques chiffrés n’est pas prise en charge.<br/><br/>**Limitation** : les clusters invités dont le disque est partagé ne sont pas pris en charge.<br/><br/>Le **port 20004** doit être ouvert sur le pare-feu local de machine virtuelle si vous souhaitez activer la cohérence de plusieurs machines virtuelles.<br/><br/>Les machines qui ont le démarrage UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) ne sont pas prises en charge.<br/><br/>Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Après avoir activé la réplication d’une machine, vous pouvez modifier le nom Azure.<br/><br/>Si la machine virtuelle source propose l’association de cartes réseau, elle est convertie en une carte réseau unique après le basculement vers Azure.<br/><br/>Si les machines virtuelles protégées disposent d’un disque iSCSI, Site Recovery convertit le disque iSCSI de machine virtuelle protégé en fichier VHD lors du basculement de la machine virtuelle vers Azure. Si la cible iSCSI peut être atteinte par la machine virtuelle Azure, elle s’y connecte et peut voir essentiellement deux disques : le disque dur virtuel de la machine virtuelle Azure et le disque source iSCSI. Dans ce cas, vous devez vous déconnecter de la cible iSCSI qui s’affiche sur la machine virtuelle Azure.
**Machines virtuelles exécutant Windows** | Les ordinateurs Windows doivent exécuter un système d’exploitation 64 bits [pris en charge](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\.<br/><br/> Le disque du système d’exploitation doit être un disque de base Windows, et non un disque dynamique. Le disque de données peut être dynamique.<br/><br/> Site Recovery prend en charge les machines virtuelles équipées d’un disque RDM. Lors de la restauration automatique, Site Recovery réutilise le disque RDM si le disque de machine virtuelle source et le disque RDM d’origine sont disponibles. Dans le cas contraire, Site Recovery crée un fichier VMDK pour chaque disque au cours de la restauration automatique.
**Machines virtuelles exécutant Linux** | Les ordinateurs Linux doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Les fichiers /etc/hosts se trouvant sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau.<br/><br/> Le nom d'hôte, les points de montage, les noms de périphériques et les chemins d'accès système et les noms de fichiers Linux (par exemple /etc/; /usr) doivent uniquement être en anglais.<br/><br/> Le stockage doit être [pris en charge](site-recovery-support-matrix.md#support-for-replicated-machines)<br/><br/> Site Recovery prend en charge les machines virtuelles équipées d’un disque RDM.  Pendant la restauration automatique de Linux, Site Recovery ne réutilise pas le disque RDM. Il crée à la place un fichier VMDK pour chaque disque RDM correspondant.<br/><br/> Vérifiez que vous avez défini le paramètre disk.enableUUID=true dans la configuration de la machine virtuelle dans VMware. Créez l’entrée si elle n’existe pas. Cette opération est nécessaire pour fournir un UUID cohérent au VMDK et lui assurer ainsi un montage correct. L’ajout de ce paramètre garantit également que seules les modifications différentielles sont retransférées en local pendant la restauration automatique, et non la réplication complète.



## <a name="physical-servers-replication-requirements-to-azure"></a>Conditions requises pour la réplication de serveurs physiques (vers Azure)

**Composant** | **Prérequis**
--- | ---
**Serveur de configuration** | Un serveur physique ou une machine virtuelle en local, qui exécute Windows Server 2012 R2. Tous les composants Site Recovery locaux sont installés sur cette machine.<br/><br/> Pour la réplication de machines physiques, la machine peut être un serveur physique.<br/><br/>  Le serveur de configuration nécessite un accès à Internet. L’accès sortant suivant est requis :<br/><br/> Accès temporaire sur HTTP 80 lors de l’installation des composants de Site Recovery (pour télécharger MySQL)<br/><br/>Accès sortant permanent sur HTTPS 443 pour la gestion de la réplication<br/><br/>Accès sortant permanent sur HTTPS 9443 pour le trafic de réplication (ce port peut être modifié)<br/><br/> Autorisez cette URL pour le téléchargement de MySQL :``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> Le serveur doit également accéder à [ces URL](#requirements-for-url-access)
**Restauration automatique** | La restauration automatique depuis Azure s’effectue toujours sur des machines virtuelles VMware, même si vous avez basculé vers un serveur physique. Si vous ne déployez pas le serveur de configuration en tant que machine virtuelle VMware, vous devez configurer une machine virtuelle VMware pour recevoir le trafic de la restauration automatique avant d’effectuer la restauration.

### <a name="physical-machine-requirements-to-azure"></a>Conditions requises pour la machine physique (vers Azure)

**Composant** | **Prérequis**
--- | ---
**Serveurs physiques exécutant Windows** | Les ordinateurs Windows doivent exécuter un système d’exploitation 64 bits [pris en charge](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\.<br/><br/> Le disque du système d’exploitation doit être un disque de base Windows, et non un disque dynamique. Le disque de données peut être dynamique.<br/><br/> Site Recovery prend en charge les machines virtuelles équipées d’un disque RDM. Lors de la restauration automatique, Site Recovery réutilise le disque RDM si le disque de machine virtuelle source et le disque RDM d’origine sont disponibles. Dans le cas contraire, Site Recovery crée un fichier VMDK pour chaque disque au cours de la restauration automatique.
**Serveurs physiques exécutant Linux** | Les ordinateurs Linux doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Les fichiers /etc/hosts se trouvant sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau.<br/><br/> Le nom d'hôte, les points de montage, les noms de périphériques et les chemins d'accès système et les noms de fichiers Linux (par exemple /etc/; /usr) doivent uniquement être en anglais.<br/><br/> Le stockage doit être [pris en charge](site-recovery-support-matrix.md#support-for-replicated-machines).

## <a name="hyper-v-replication-requirements-to-azure"></a>Conditions requises pour la réplication Hyper-V (vers Azure)

**Composant** | **Prérequis**
--- | ---
**VMM (facultatif)** | Facultativement, vous pouvez répliquer des machines virtuelles sur des hôtes Hyper-V gérés dans des clouds VMM.<br/><br/> Si vous n’utilisez pas VMM, vous regroupez un ou plusieurs hôtes Hyper-V ou clusters dans des sites Hyper-V et configurez la réplication pour un site.<br/><br/> Si vous utilisez VMM, il doit s’exécuter sur System Center 2012 R2.<br/><br/> Si vous utilisez VMM, chaque serveur VMM doit être associé à un ou plusieurs clouds configurés. Un cloud doit contenir un ou plusieurs groupes hôtes VMM et un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.<br/><br/> Le serveur VMM doit être connecté à Internet, avec un accès aux [URL requises](#requirements-for-url-access).<br/><br/> Si le serveur VMM inclut des règles de pare-feu basées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/><br/> Autorisez [les plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest.
**Hôtes Hyper-V** | Les serveurs hôtes Hyper-V doivent exécuter au moins **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2**, et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/>Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br/><br/>Les correctifs mentionnés dans l’article [2961977](https://support.microsoft.com/kb/2961977) doivent être installés sur les serveurs Hyper-V.<br/><br/>Les serveurs hôte Hyper-V doivent pouvoir accéder à Internet pour gérer la réplication de données vers Azure, notamment un accès aux [URL requises](#requirements-for-url-access). Autorisez également [les plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest.<br/><br/> Si des ordinateurs hôtes Hyper-V se trouvent dans des clouds VMM, veillez à installer le correctif décrit dans l’article de la base de connaissances [KB 2961977](https://support.microsoft.com/en-us/kb/2961977)

### <a name="hyper-v-vm-requirements-to-azure"></a>Conditions requises pour la machine virtuelle Hyper-V (vers Azure)

**Composant** | **Prérequis**
--- | ---
**Conformité de la machine virtuelle** |Avant de basculer vers une machine virtuelle, vous devez vous assurer que le nom attribué à la machine virtuelle Azure est conforme à la [configuration requise pour Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Le cas échéant, vous pouvez modifier le nom une fois la réplication activée pour la machine virtuelle.
**Disque** | Sur les machines protégées, la capacité d’un disque ne doit pas dépasser 1023 Go. Une machine virtuelle peut comporter jusqu’à 64 disques (jusqu’à 64 To).<br/><br/> Les clusters invités dont le disque est partagé ne sont pas pris en charge.
**UEFI** | L’amorçage UEFI (Unified Extensible Firmware Interface) / EFI (Extensible Firmware Interface) n’est pas pris en charge.
**Association de cartes réseau** | Si la machine virtuelle source propose l’association de cartes réseau, elle est convertie en une carte réseau unique après le basculement vers Azure.
**Statique Linux** | La protection des machines virtuelles Hyper-V exécutant Linux avec une adresse IP statique n’est pas prise en charge.



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>Conditions requises pour la réplication de serveurs VMware/physiques (site secondaire)

Ce scénario est géré par le produit InMage, qui peut être téléchargé à partir du coffre Site Recovery lorsque vous choisissez de répliquer des machines virtuelles VMware ou des serveurs physiques sur un site secondaire. Notez que ce scénario n’est pas disponible dans le portail Azure.

**Composant** | **Prérequis**
--- | ---
**Site principal** | Vous avez besoin d’un serveur de traitement pour gérer la mise en cache, la compression et l’optimisation des données. Ce serveur gère également l’installation push de l’agent unifié qui est nécessaire sur chaque ordinateur que vous souhaitez répliquer.<br/><br/> Si vous souhaitez répliquer des machines virtuelles VMware, vous avez besoin d’un ou plusieurs serveurs VMware ESX/ESXi et éventuellement d’un serveur VMware vCenter.
**Site secondaire** | Vous avez besoin d’un serveur de configuration installé sur le site secondaire pour configurer et gérer votre environnement.<br/><br/> Un serveur cible maître est installé sur le site secondaire pour contenir les données répliquées.<br/><br/> Un serveur vContinuum est installé par défaut sur le serveur cible maître et fournit une console pour gérer et surveiller votre environnement.

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>Conditions requises pour une machine virtuelle VMware/physique (site secondaire)

Vérifiez les conditions requises dans la [Matrice de prise en charge InMage](https://aka.ms/asr-scout-cm).


## <a name="hyper-v-replication-requirements-secondary-site"></a>Conditions requises pour la réplication Hyper-V (site secondaire)

**Composant** | **Prérequis**
--- | ---
**VMM** | Pour répliquer des machines virtuelles Hyper-V sur un site secondaire, l’hôte Hyper-V doit être géré dans des clouds System Center VMM. <br/><br/> VMM doit exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Nous recommandons un serveur VMM dans le site principal et un autre dans le site secondaire. Vous pouvez répliquer entre [différents clouds sur le même serveur VMM](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm), mais une configuration manuelle est nécessaire dans ce scénario.<br/><br/> Le serveur VMM doit être connecté à Internet, avec un accès aux [URL requises](#requirements-for-url-access).<br/><br/> Si le serveur VMM inclut des règles de pare-feu basées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/><br/> Autorisez [les plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest.
**Hyper-V** | Les serveurs Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour installées.<br/><br/> Le serveur Hyper-V doit contenir au moins une machine virtuelle<br/><br/> Les hôtes Hyper-V doivent être situés dans des groupes hôtes sur les serveurs VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster Windows Server 2012 R2, vous devez installer la mise à jour [2961977](https://support.microsoft.com/kb/2961977). Si vous disposez d’un cluster Hyper-V sous Windows Server 2012, le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. [En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) pour configurer manuellement.

### <a name="hyper-v-vm-requirements-secondary-site"></a>Conditions requises pour la machine virtuelle Hyper-V (site secondaire)

**Composant** | **Détails**
--- | ---
**Cloud VMM** | Les machines virtuelles doivent se trouver sur l’hôte Hyper-V dans des clouds VMM.






## <a name="url-access-requirements"></a>Conditions requises pour l’accès URL

Ces URL doivent être disponibles depuis VMware, VMM et les serveurs hôtes Hyper-V.

**URL** | **VMM vers VMM** | **VMM vers Azure** | **Hyper-V vers Azure** | **VMware vers Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | AUTORISER | Autoriser | Autoriser | Autoriser
``*.backup.windowsazure.com`` | Non requis | Autoriser | Autoriser | Autoriser
``*.hypervrecoverymanager.windowsazure.com`` | Autoriser | Autoriser | Autoriser | Autoriser
``*.store.core.windows.net`` | Autoriser | Autoriser | Autoriser | Autoriser
``*.blob.core.windows.net`` | Non requis | Autoriser | Autoriser | Autoriser
``https://www.msftncsi.com/ncsi.txt`` | Autoriser | Autoriser | Autoriser | Autoriser
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Non requis | Non requis | Non requis | Autoriser le téléchargement SQL
``time.windows.com`` | AUTORISER | Autoriser | Autoriser | Autoriser
``time.nist.gov`` | Autoriser | Autoriser | Autoriser | AUTORISER

## <a name="failover-requirements"></a>Conditions requises pour le basculement

**Scénario** | **Test** | **Planifié** | **Non planifié**
--- | --- | --- | ---
VMware vers Azure | Pris en charge | Non pris en charge | Pris en charge
Physique vers Azure | Pris en charge | Non pris en charge | Pris en charge
Hyper-V (VMM) vers Azure | Pris en charge | Pris en charge | Pris en charge
Hyper-V (sans VMM) vers Azure | Pris en charge | Pris en charge | Non pris en charge
Hyper-V (VMM principal/cloud) vers secondaire | Pris en charge | Pris en charge | Pris en charge

## <a name="failback-requirements"></a>Conditions requises pour la restauration automatique

**Scénario** | **Test** | **Planifié** | **Non planifié**
--- | --- | --- | ---
**Azure vers VMware** | Non pris en charge | Non pris en charge | Pris en charge
**Azure vers physique** | Non pris en charge | Non pris en charge | Pris en charge pour VMware uniquement
**Azure vers VMM** | Non pris en charge | Pris en charge | Non pris en charge
**Azure vers Hyper-V (sans VMM)** | Non pris en charge | Pris en charge | Non pris en charge
**Hyper-V (VMM secondaire/cloud) vers principal** | Pris en charge | Pris en charge | Pris en charge

### <a name="failback-from-azure-to-vmware-vms"></a>Restauration automatique entre Azure et des machines virtuelles VMware

**Composant** | **Détails**
--- | ---
**Serveur de traitement temporaire dans Azure** | Si vous souhaitez effectuer une restauration automatique à partir d’Azure après le basculement, vous devez définir une machine virtuelle Azure configurée comme serveur de traitement pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.
**Connexion VPN** | Pour une restauration automatique, vous devez disposer d’une connexion VPN (ou Azure ExpressRoute) configurée à partir du réseau Azure sur le site local.
**Serveur cible maître local distinct** | Le serveur cible maître local gère la restauration automatique. Le serveur cible maître est installé par défaut sur le serveur d’administration. Toutefois, si vous restaurez automatiquement de plus grands volumes de trafic vous devez définir un serveur cible maître local à cet effet.



### <a name="failback-from-azure-to-physical-machines"></a>Restauration automatique à partir d’Azure sur des machines physiques

**Composant** | **Détails**
--- | ---
**Restauration automatique physique à physique** | Ceci n’est pas pris en charge. Cela signifie que si vous basculez les serveurs physiques vers Azure, puis souhaitez les restaurer automatiquement, vous devez le faire vers une machine virtuelle VMware. Il est impossible d’effectuer cette procédure vers un serveur physique.
**Machine virtuelle** | Vous avez besoin d’une machine virtuelle VMware locale sur laquelle effectuer la restauration automatique.
**Serveur de configuration** | Si vous ne déployez pas le serveur de configuration local en tant que machine virtuelle VMware, vous devez configurer un serveur cible maître distinct en tant que machine virtuelle VMware. C’est nécessaire, car le serveur cible maître interagit sur et s’attache au stockage VMware pour restaurer les disques vers une machine virtuelle VMware.



## <a name="deployment-optimization"></a>Optimisation du déploiement

Pour optimiser et mettre à l’échelle votre déploiement, tenez compte des conseils suivants.

- **Taille du volume du système d’exploitation**: quand vous répliquez une machine virtuelle dans Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
- **Taille du disque de données** : si vous répliquez vers Azure, vous pouvez avoir jusqu’à 64 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ 64 To.
- **Limites de plan de récupération**: Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
- **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de limites par défaut liées aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
- **Planification de la capacité**: en savoir plus sur la [planification de la capacité](site-recovery-capacity-planner.md) pour Site Recovery.
- **Bande passante de réplication**: si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
- **ExpressRoute**: Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed. [En savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
- **Trafic de réplication**: Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
- **Trafic réseau**: pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de [Qualité de service (QoS) Windows](https://technet.microsoft.com/library/hh967468.aspx) à l’aide d’une stratégie basée sur l’adresse IP et le port de destination.  De plus, si vous répliquez vers Azure Site Recovery à l’aide de l’agent Azure Backup, vous pouvez lui appliquer une limitation. [En savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO**: pour mesurer l’objectif de délai de récupération procuré par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les tâches de récupération afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
- **RPO**: Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Azure.



## <a name="next-steps"></a>Étapes suivantes
Après avoir examiné les exigences de déploiement générales, lisez les conditions requises détaillées et déployez un scénario.

* [Répliquez des machines virtuelles VMware sur Microsoft Azure.](site-recovery-vmware-to-azure.md)
* [Répliquer des serveurs physiques dans Azure](site-recovery-vmware-to-azure.md)
* [Répliquer un serveur Hyper-V dans des clouds VMM vers Azure](site-recovery-vmm-to-azure.md)
* [Réplication de machines virtuelles Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md)
* [Réplication de machines virtuelles Hyper-V vers un site secondaire](site-recovery-vmm-to-vmm.md)
* [Réplication de machines virtuelles Hyper-V vers un site secondaire avec SAN](site-recovery-vmm-san.md)
* [Réplication de machines virtuelles Hyper-V avec un seul serveur VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


