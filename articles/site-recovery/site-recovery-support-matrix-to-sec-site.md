---
title: "Matrice de prise en charge pour la réplication sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: c0f86e13e21f2af323e0a306b381054b6eb76755
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matrice de prise en charge pour la réplication sur un site secondaire avec Azure Site Recovery

Cet article résume ce qui est pris en charge lorsque vous utilisez le service [Azure Site Recovery](site-recovery-overview.md) pour répliquer sur un site secondaire local.

## <a name="supported-scenarios"></a>Scénarios pris en charge

**Déploiement** | **Détails** 
--- | ---
**VMware vers VMware** | Récupération d’urgence de machines virtuelles VMware locales vers un site VMware secondaire.<br/><br/> Télécharger le [guide de l’utilisateur InMage Scout](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
**Hyper-V vers Hyper-V** | Récupération d’urgence de machines virtuelles Hyper-V locales résidant dans des clouds VMM vers un cloud VMM secondaire.<br></br> Non pris en charge sans VMM.



  

## <a name="host-servers"></a>Serveurs hôtes

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement) 
**Hyper-V avec VMM** | Windows Server 2016 et Windows Server 2012 R2 avec les dernières mises à jour.<br/><br/> Les hôtes Windows Server 2016 doivent être gérés par VMM 2016.<br/><br/> Les clouds VMM 2016 qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge.<br/><br/> Les déploiements comprenant la mise à niveau d’une VMM 2012 R2 existant vers System Center 2016 ne sont actuellement pas pris en charge.


## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée

Le tableau suivant récapitule la prise en charge du système d’exploitation pour les machines répliquées avec Site Recovery. Toute charge de travail peut être exécutée sur le système d’exploitation pris en charge.

**Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | ---
Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Tout système d’exploitation invité [pris en charge par Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Stockage de machine Linux

Seules les machines Linux avec le stockage suivant peuvent être répliquées :

- Système de fichiers (EXT3, ETX4, ReiserFS, XFS).
- Logiciel multichemin-device Mapper.
- Gestionnaire de volume (LVM2).
- Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge.
- Le système de fichiers ReiserFS n’est pris en charge que sur SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Configuration réseau

### <a name="hosts"></a>Hôtes

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Association de cartes réseau | Oui | Oui
VLAN | Oui | Oui
IPv4 | Oui | Oui
IPv6 | Non | Non

### <a name="guest-vms"></a>MV invitées

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Association de cartes réseau | Non | Non
IPv4 | Oui | Oui
IPv6 | Non | Non
Adresse IP statique (Windows) | Oui | Oui
Adresse IP statique (Linux) | Oui | Oui
Plusieurs cartes réseau | Oui | Oui


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Stockage hôte

**Stockage (hôte)** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
NFS | Oui | N/A
SMB 3.0 | N/A | Oui
SAN (ISCSI) | Oui | Oui
Chemins d’accès multiples (MPIO) | Oui | Oui

### <a name="guest-or-physical-server-storage"></a>Stockage sur serveur physique ou invité

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
VMDK | Oui | N/A
VHD/VHDX | N/A | Oui (jusqu’à 16 disques)
Machine virtuelle de 2e génération | N/A | Oui
Disque de cluster partagé | Oui  | Non
Disque chiffré | Non | Non
UEFI| Non | N/A
NFS | Non | Non
SMB 3.0 | Non | Non
RDM | Oui | N/A
Disque > 1 To | Non | Oui
Volume avec disque à bandes > 1 To<br/><br/> LVM | Oui | Oui
Espaces de stockage | Non | Oui
Ajout/suppression de disque à chaud | Non | Non
Exclure le disque | Non | Oui
Chemins d’accès multiples (MPIO) | N/A | Oui

## <a name="vaults"></a>Coffres

**Action** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Déplacer les coffres entre plusieurs groupes de ressources (dans ou entre les différents abonnements) | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources (dans ou entre les différents abonnements) | Non | Non

## <a name="provider-and-agent"></a>Fournisseur et agent

**Nom** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Installé sur des serveurs VMM locaux ou des serveurs Hyper-V, si aucun serveur VMM n’existe | 5.1.19 ([disponible sur le portail](http://aka.ms/downloaddra)) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware locaux ou les serveurs physiques et le site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer  | N/A (disponible sur le portail) | N/A


## <a name="next-steps"></a>Étapes suivantes

- [Répliquer des machines virtuelles Hyper-V résidant dans des clouds VMM vers un site secondaire](tutorial-vmm-to-vmm.md)
- [Répliquer des machines virtuelles VMware et des serveurs physiques vers un site secondaire](tutorial-vmware-to-vmware.md)
