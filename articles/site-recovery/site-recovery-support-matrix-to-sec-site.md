---
title: "Matrice de prise en charge pour la réplication sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/08/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: d53d4cfdc7b673d2816fa9372dedbed540380cce
ms.lasthandoff: 04/04/2017


---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matrice de prise en charge pour la réplication sur un site secondaire avec Azure Site Recovery

> [!div class="op_single_selector"]
> * [Réplication vers Azure](site-recovery-support-matrix-to-azure.md)
> * [Réplication vers un emplacement local](site-recovery-support-matrix-to-sec-site.md)

Cet article résume ce qui est pris en charge lorsque vous utilisez Azure Site Recovery pour répliquer sur un site secondaire local.

## <a name="deployment-options"></a>Options de déploiement

**Déploiement** | **Serveur VMware/physique** | **Hyper-V (avec ou sans SCVMM)
--- | --- | --- | ---
**Portail Azure** | Machines virtuelles VMware locales vers site VMware secondaire.<br/><br/> Téléchargez le [guide de l’utilisateur InMage Scout](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) (non disponible dans le portail Azure). | Machines virtuelles Hyper-V locales résidant dans des clouds VMM vers un cloud VMM secondaire.<br></br> Non pris en charge sans SCVMM  <br/><br/> Réplication Hyper-V standard uniquement. SAN non pris en charge.
**Portail classique** | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Mode Maintenance uniquement<br></br> Non pris en charge sans SCVMM
**PowerShell** | Non pris en charge | Pris en charge<br></br> Non pris en charge sans SCVMM

## <a name="on-premises-servers"></a>Serveurs locaux

### <a name="virtualization-servers"></a>Serveurs de virtualisation

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour
**Hyper-V (avec VMM)** | VMM 2016 et VMM 2012 R2

  >[!Note]
  > Les clouds VMM 2016 qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge.

### <a name="host-servers"></a>Serveurs hôtes

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement) 
**Hyper-V (sans VMM)** | Configuration non prise en charge pour la réplication vers un site secondaire
**Hyper-V avec VMM** | Windows Server 2016 et Windows Server 2012 R2 avec les dernières mises à jour.<br/><br/> Les hôtes Windows Server 2016 doivent être gérés par VMM 2016.

## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée
Le tableau ci-dessous récapitule la prise en charge des systèmes d’exploitation dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery. Cette prise en charge est applicable pour toutes les charges de travail en cours d’exécution sur le système d’exploitation mentionné.

**Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Tout système d’exploitation invité [pris en charge par Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>Seules les machines Linux avec le stockage suivant peuvent être répliquées : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil ; gestionnaire de volume (LVM2).
>Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge.
>Le système de fichiers ReiserFS n’est pris en charge que sur SUSE Linux Enterprise Server 11 SP3.

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

En savoir plus sur les [Conditions préalables au déploiement](site-recovery-prereq.md).

