---
title: Matrice de prise en charge Azure Site Recovery | Microsoft Docs
description: "Résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/20/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 0c89357b2d6e2ab92d6a4a1b771650d77edb5e66
ms.openlocfilehash: 78716196cb677c95dae5082face6c8ea6499df4b


---
# <a name="azure-site-recovery-support-matrix"></a>Matrice de prise en charge Azure Site Recovery

Cet article résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery. Une liste des conditions préalables et des composants pris en charge est disponible pour chaque scénario de déploiement dans l’article de déploiement correspondant, et ce document résume l’ensemble.

## <a name="support-for-virtualization-server-operating-systems"></a>Prise en charge de systèmes d’exploitation de serveurs de virtualisation

### <a name="host-servers-replicate-to-azure"></a>Serveurs hôtes (réplication vers Azure)

**Machine virtuelle VMware/serveur physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | ---
vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement)  <br/><br/>  vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour | Windows Server 2012 R2 avec les dernières mises à jour | Windows Server 2012 R2 avec les dernières mises à jour

### <a name="host-servers-replicate-to-secondary-site"></a>Serveurs hôtes (réplication vers un site secondaire)

**Machine virtuelle VMware/serveur physique** | **Hyper-V (avec VMM)**
--- | --- | ---
vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement)  <br/><br/>  vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour | Au minimum Windows Server 2012 avec les dernières mises à jour


## <a name="support-for-replicated-machines"></a>Prise en charge des machines répliquées

### <a name="machines-replicate-to-azure"></a>Machines (réplication vers Azure)

Les machines virtuelles doivent respecter la [configuration requise pour Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

**Prérequis** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Ce qui est répliqué | Toute charge de travail sur une machine virtuelle Windows ou Linux | Toute charge de travail | Toute charge de travail
Système d’exploitation hôte | Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/>  Stockage requis : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie)) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3. | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx) | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)


### <a name="machines-replicate-to-secondary-site"></a>Machines (réplication vers un site secondaire)

**Prérequis** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Ce qui est répliqué | Toute charge de travail sur une machine virtuelle Windows ou Linux | Toute charge de travail | Toute charge de travail
Système d’exploitation hôte | Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/>  Stockage requis : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie)) ; gestionnaire de volume : (LVM2).<br/><br/> Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3. | Tout système d’exploitation invité pris en charge par Hyper-V] (https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>Prise en charge du fournisseur et de l’agent

**Nom** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre des serveurs locaux et le site Azure/secondaire  <br/><br/>  Installé sur des serveurs VMM locaux ou des serveurs Hyper-V, si aucun serveur VMM n’existe | 5.1.1700 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Installation unifiée d’Azure Site Recovery (VMware vers Azure)** | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/>  Installé sur des serveurs VMware locaux | 9.3.4246.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer.  | N/A (disponible sur le portail) | .
**Agent Microsoft Azure Recovery Services (MARS)** | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/>  Installé sur des serveurs Hyper-V locaux (avec ou sans serveur VMM) | |

## <a name="support-for-networking"></a>Prise en charge de la mise en réseau

### <a name="networking-replicate-to-azure"></a>Mise en réseau (réplication vers Azure)

**Mise en réseau hôte** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Association de cartes réseau | Oui<br/><br/>Non pris en charge dans des machines physiques| Oui | Oui
VLAN | Oui | Oui | Oui
IPv4 | Oui | Oui | Oui
IPv6 | Non | Non | Non

**Mise en réseau d’une machine virtuelle invitée** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Association de cartes réseau | Non | Non | Non
IPv4 | Oui | Oui | Oui
IPv6 | Non | Non | Non
Adresse IP statique (Windows) | Oui | Oui | Oui
Adresse IP statique (Linux) | Non | Non | Non
Plusieurs cartes réseau | Oui | Oui | Oui

**Mise en réseau Azure** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
ExpressRoute | Oui | Oui | Oui
ILB | Oui | Oui | Oui
ELB | Oui |  |
Traffic Manager | Oui | Oui | Oui
Plusieurs cartes réseau | Oui | Oui | Oui
Adresse IP réservée | Oui | Oui | Oui
IPv4 | Oui | Oui | Oui
Conserver l’adresse IP source | Oui | Oui | Oui

### <a name="networking-replicate-to-secondary-site"></a>Mise en réseau (réplication vers un site secondaire)

**Mise en réseau hôte** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Association de cartes réseau | Oui | Oui
VLAN | Oui | Oui
IPv4 | Oui | Oui
IPv6 | Non | Non

**Mise en réseau d’une machine virtuelle invitée** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Association de cartes réseau | Non | Non
IPv4 | Oui | Oui
IPv6 | Non | Non
Adresse IP statique (Windows) | Oui | Oui
Adresse IP statique (Linux) | Oui | Oui
Plusieurs cartes réseau | Oui | Oui


## <a name="support-for-storage"></a>Prise en charge du stockage

### <a name="storage-replicate-to-azure"></a>Stockage (réplication vers Microsoft Azure)

**Stockage (hôte)** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques | N/D | N/D
SMB 3.0 | N/D | Oui | Oui
SAN (ISCSI) | Oui | Oui | Oui
Chemins d’accès multiples (MPIO) | Oui pour VMware<br/><br/> NA pour les serveurs physiques | Oui | Oui

**Stockage (machine virtuelle invitée/serveur physique)** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
VMDK | Oui | N/D | N/D
VHD/VHDX | N/D | Oui | Oui
Machine virtuelle de 2e génération | N/D | Oui | Oui
Disque de cluster partagé | Oui pour VMware<br/><br/> NA pour les serveurs physiques | Non | Non
Disque chiffré | Non | Non | Non
EFI/UEFI| Non | Oui | Oui
NFS | Non | Non | Non
SMB 3.0 | Non | Non | Non
RDM | Oui<br/><br/> NA pour les serveurs physiques | N/D | N/D
Disque > 1 To | Non | Non | Non
Volume avec disque à bandes > 1 To<br/><br/> LVM | Oui | Oui | Oui
Espaces de stockage | Non | Oui | Oui
Ajout/suppression de disque à chaud | Non | Non | Non
Exclure le disque | Oui | Non | Non
Chemins d’accès multiples (MPIO) | N/D | Oui | Oui

**Stockage Azure** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
LRS | Oui | Oui | Oui
GRS | Oui | Oui | Oui
Stockage froid | Non | Non | Non
Stockage chaud| Non | Non | Non
Chiffrement au repos | Oui | Oui | Oui
Stockage Premium | Oui | Non | Non
Service Import/Export | Non | Non | Non


### <a name="storage-replicate-to-secondary-site"></a>Stockage (réplication vers un site secondaire)

**Stockage (hôte)** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
NFS | Oui | N/D
SMB 3.0 | N/D | Oui
SAN (ISCSI) | Oui | Oui
Chemins d’accès multiples (MPIO) | Oui | Oui

**Stockage (machine virtuelle invitée/serveur physique)** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
VMDK | Oui | N/D
VHD/VHDX | N/D | Oui (jusqu'à 64 disques)
Machine virtuelle de 2e génération | N/D | Oui
Disque de cluster partagé | Oui  | Non
Disque chiffré | Non | Non
UEFI| Non | N/D
NFS | Non | Non
SMB 3.0 | Non | Non
RDM | Oui | N/D
Disque > 1 To | Non | Oui
Volume avec disque à bandes > 1 To<br/><br/> LVM | Oui | Oui
Espaces de stockage | Non | Oui
Ajout/suppression de disque à chaud | Non | Non
Exclure le disque | Non | Non
Chemins d’accès multiples (MPIO) | N/D | Oui

## <a name="support-for-recovery-services-vault-actions"></a>Prise en charge des actions de coffre Recovery Services

### <a name="vaults-replicate-to-azure"></a>Coffres (réplication vers Microsoft Azure)

**Action** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non | Non

### <a name="vaults-replicate-to-secondary-site"></a>Coffres (réplication vers un site secondaire)

**Action** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non


## <a name="support-for-azure-compute-replicate-to-azure"></a>Prise en charge du calcul Azure (réplication vers Azure)

**Fonctionnalité de calcul** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Clusters invités avec disque partagé | Non | Non | Non
Groupes à haute disponibilité | Non | Non | Non
CONCENTRATEUR | Oui | Oui | Oui









## <a name="next-steps"></a>Étapes suivantes
[Préparer le déploiement](site-recovery-best-practices.md)



<!--HONumber=Nov16_HO5-->


