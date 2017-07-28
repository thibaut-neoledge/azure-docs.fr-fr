---
title: "Matrice de support Azure Site Recovery pour la réplication vers Azure | Microsoft Docs"
description: "Résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 6664cb20393ec5f588c8eeb119d6f606a0072861
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matrice de support Azure Site Recovery pour la réplication de machines virtuelles locales vers Azure


Cet article résume les composants et les configurations pris en charge pour Azure Site Recovery lors de la réplication et de la récupération vers Azure. Pour plus d’informations sur les conditions requises pour Azure Site Recovery, consultez la [configuration requise](site-recovery-prereq.md).


## <a name="support-for-deployment-options"></a>Prise en charge des options de déploiement

**Déploiement** | **Serveur VMware/physique** | **Hyper-V (avec / sans  Virtual Machine Manager)** |
--- | --- | ---
**Portail Azure** | Machines virtuelles VMware locales vers stockage Azure, avec Azure Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques. | Machines virtuelles Hyper-V locales vers stockage Azure, avec Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques.
**Portail classique** | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Mode Maintenance uniquement.
**PowerShell** | Non pris en charge pour le moment. | Pris en charge


## <a name="support-for-datacenter-management-servers"></a>Prise en charge des serveurs de gestion du centre de données

### <a name="virtualization-management-entities"></a>Entités de gestion de la virtualisation

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour
**Hyper-V (avec VMM)** | System Center Virtual Machine Manager 2016 et System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Un cloud System Center Virtual Machine Manager 2016 qui combine des hôtes Windows Server 2016 et 2012 R2 n’est pas actuellement pris en charge.

### <a name="host-servers"></a>Serveurs hôtes

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement) 
**Hyper-V (avec / sans Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 avec les dernières mises à jour.<br></br>Si SCVMM est utilisé, les hôtes Windows Server 2016 doivent être gérés par SCVMM 2016.


  >[!Note]
  >Les sites Hyper-V qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge. La récupération vers un autre emplacement pour les machines virtuelles sur un hôte Windows Server 2016 n’est pas prise en charge pour le moment.

## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée

Les machines virtuelles qui sont protégées doivent répondre aux [conditions requises pour Azure](#failed-over-azure-vm-requirements) lors de la réplication vers Azure.
Le tableau ci-dessous récapitule la prise en charge des systèmes d’exploitation répliqués dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery. Cette prise en charge est applicable pour toutes les charges de travail en cours d’exécution sur le système d’exploitation mentionné.

 **Serveur VMware/physique** | **Hyper-V (avec ou sans VMM)** |
--- | --- |
Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2 <br/><br/>CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2 <br/><br/>Serveur LTS Ubuntu 14.04[ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> SUSE Linux Enterprise Server 11 SP4 <br/>(La mise à niveau des machines de réplication de SLES 11 SP3 vers SLES 11 SP4 n’est pas prise en charge. Si une machine répliquée a été mise à niveau, de SLES 11SP3 vers SLES 11 SP4, vous devez désactiver la réplication et protéger à nouveau la machine après la mise à niveau.) | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!IMPORTANT]
>(Applicable aux serveurs physiques / VMware avec réplication vers Azure)
>
> Sur les serveurs Red Hat Enterprise Linux Server 7+ et CentOS 7+, la version de noyau 3.10.0-514 est prise en charge à partir de la version 9.8 du service Mobilité Azure Site Recovery.<br/><br/>
> Les clients sur le noyau 3.10.0-514 avec une version du service Mobilité inférieure à la version 9.8 doivent désactiver la réplication, installer la version 9.8 du service Mobilité et réactiver la réplication.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versions du noyau Ubuntu prises en charge pour les serveurs VMware/physiques

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic à 3.13.0-117-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-75-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Systèmes de fichiers pris en charge et configurations de stockage invité sous Linux (serveurs physiques / VMware)

Les systèmes de fichiers et le logiciel de configuration de stockage suivants sont pris en charge sur les serveurs Linux exécutés sur des serveurs VMware ou physiques :
* Systèmes de fichiers : ext3, ext4, ReiserFS (Suse Linux Enterprise Server uniquement), XFS (jusqu’à v4 uniquement)
* Gestionnaire de volume : LVM2
* Logiciel multichemin : Device Mapper

Les serveurs physiques avec le contrôleur de stockage HP CCISS ne sont pas pris en charge.

>[!Note]
> Sur les serveurs Linux, les répertoires suivants (s’ils sont configurés en tant que partitions / systèmes de fichiers séparés) doivent tous se trouver sur le même disque (le disque du système d’exploitation) sur le serveur source : / (racine), /boot, / usr, / usr/local, / var, /etc.<br/><br/>
> Les fonctionnalités de XFS v5 telles que les sommes de contrôle des métadonnées ne sont actuellement pas prises en charge par ASR sur les systèmes de fichiers XFS. Assurez-vous que vos systèmes de fichiers XFS n’utilisent pas de fonctionnalités v5. Vous pouvez utiliser l’utilitaire xfs_info pour vérifier le superbloc XFS pour la partition. Si ftype est défini sur 1, les fonctionnalités XFSv5 sont utilisées.
>


## <a name="support-for-network-configuration"></a>Prise en charge de la configuration réseau
Les tableaux suivants récapitulent la prise en charge de la configuration réseau dans différents scénarios de déploiement avec utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-network-configuration"></a>Configuration du réseau hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
Association de cartes réseau | Oui<br/><br/>Non pris en charge dans des machines physiques| Oui
VLAN | Oui | Oui
IPv4 | Oui | Oui
IPv6 | Non | Non

### <a name="guest-vm-network-configuration"></a>Configuration du réseau de machines virtuelles invitées

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
Association de cartes réseau | Non | Non
IPv4 | Oui | Oui
IPv6 | Non | Non
Adresse IP statique (Windows) | Oui | Oui
Adresse IP statique (Linux) | Non | Non
Plusieurs cartes réseau | Oui | Oui

### <a name="failed-over-azure-vm-network-configuration"></a>Configuration de réseau des machines virtuelles Azure basculées

**Mise en réseau Azure** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | Oui | Oui
ILB | Oui | Oui
ELB | Oui | Oui
Traffic Manager | Oui | Oui
Plusieurs cartes réseau | Oui | Oui
Adresse IP réservée | Oui | Oui
IPv4 | Oui | Oui
Conserver l’adresse IP source | Oui | Oui


## <a name="support-for-storage"></a>Prise en charge du stockage
Les tableaux suivants récapitulent la prise en charge de la configuration du stockage dans différents scénarios de déploiement avec utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-storage-configuration"></a>Configuration du stockage hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques | N/A
SMB 3.0 | N/A | Oui
SAN (ISCSI) | Oui | Oui
Chemins d’accès multiples (MPIO)<br></br>Testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pour CLARiiON | Oui | Oui

### <a name="guest-or-physical-server-storage-configuration"></a>Configuration du stockage sur serveur physique ou invité

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
VMDK | Oui | N/A
VHD/VHDX | N/A | Oui
Machine virtuelle de 2e génération | N/A | Oui
EFI/UEFI| Non | Oui
Disque de cluster partagé | Oui pour VMware<br/><br/> N/A pour les serveurs physiques | Non
Disque chiffré | Non | Non
NFS | Non | N/A
SMB 3.0 | Non | Non
RDM | Oui<br/><br/> N/A pour les serveurs physiques | N/A
Disque > 1 To | Non | Non
Volume avec disque à bandes > 1 To<br/><br/> Gestion des volumes logiques | Oui | Oui
Espaces de stockage | Non | Oui
Ajout/suppression de disque à chaud | Non | Non
Exclure le disque | Oui | Oui
Chemins d’accès multiples (MPIO) | N/A | Oui

**Stockage Azure** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
LRS | Oui | Oui
GRS | Oui | Oui
RA-GRS | Oui | Oui
Stockage froid | Non | Non
Stockage chaud| Non | Non
Chiffrement au repos (SSE)| Oui | Oui
Stockage Premium | Oui | Oui
Service Import/Export | Non | Non


## <a name="support-for-azure-compute-configuration"></a>Prise en charge de la configuration de calcul Azure

**Fonctionnalité de calcul** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | --- | ---
Groupes à haute disponibilité | Oui | Oui
HUB | Oui | Oui  

## <a name="failed-over-azure-vm-requirements"></a>Configuration requise des machines virtuelles Azure basculées

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Les machines virtuelles locales à répliquer doivent répondent à la configuration requise d’Azure ci-dessous lors de la réplication vers Azure.

**Entité** | **Configuration requise** | **Détails**
--- | --- | ---
**Système d’exploitation invité** | Pour une réplication de Hyper-V sur Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de VMware et de serveur physique, vérifiez les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Architecture du système d’exploitation invité** | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque du système d’exploitation** | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques du système d’exploitation** | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques de données** | 64 ou moins si vous répliquez des **machines virtuelles VMware sur Azure** ; 16 ou moins si vous répliquez des **machines virtuelles Hyper-V sur Azure** | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque dur virtuel de données** | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Adaptateurs réseau** | Prise en charge de plusieurs adaptateurs réseau. |
**Disque dur virtuel partagé** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque FC** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Format de disque dur** | Disque dur virtuel (VHD)  <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
**BitLocker** | Non pris en charge | Bitlocker doit être désactivé préalablement à la protection d’une machine virtuelle.
**Nom de la machine virtuelle** | Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Le nom de la machine virtuelle doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery.
**Type de machine virtuelle** | Génération 1<br/><br/> Génération 2 -- Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et un espace disque inférieur à 300 Go sont prises en charge.<br></br>Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Prise en charge des actions de coffre Recovery Services

**Action** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non | Non


## <a name="support-for-provider-and-agent"></a>Prise en charge du fournisseur et de l’agent

**Nom** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Installé sur les serveurs VMM locaux ou sur des serveurs Hyper-V s’il n’existe aucun serveur VMM | 5.1.19 ([disponible sur le portail](http://aka.ms/downloaddra)) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Installation unifiée d’Azure Site Recovery (VMware vers Azure)** | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/> Installé sur des serveurs VMware locaux | 9.3.4246.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer  | N/A (disponible sur le portail) | N/A
**Agent Microsoft Azure Recovery Services (MARS)** | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V locaux (avec ou sans serveur VMM) | Dernier agent ([disponible sur le portail](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Étapes suivantes
[Vérifiez les composants requis](site-recovery-prereq.md)

