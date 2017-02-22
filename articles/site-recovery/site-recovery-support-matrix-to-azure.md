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
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 3b9912ac45627f799ef3ca20eccf8d11f9ae52f0


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Matrice de support Azure Site Recovery pour la réplication vers Azure

> [!div class="op_single_selector"]
> * [Réplication vers Azure](site-recovery-support-matrix-to-azure.md)
> * [Réplication vers un site secondaire appartenant au client](site-recovery-support-matrix-to-sec-site.md)


Une liste des conditions préalables pour Azure Site Recovery est mentionnée [ici](site-recovery-best-practices.md) et l’article ci-dessous résume les configurations et les composants pris en charge pour Azure Site Recovery lors de la réplication et de la récupération pour Azure.


## <a name="support-for-deployment-options"></a>Prise en charge des options de déploiement

**Déploiement** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
**Portail Azure** | Machines virtuelles VMware locales vers stockage Azure, avec Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques. | Machines virtuelles Hyper-V locales (hors clouds de VMM) vers stockage Azure, avec Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques. | Machines virtuelles Hyper-V locales (dans des clouds de VMM) vers stockage Azure, avec Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques.
**Portail classique** | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Mode Maintenance uniquement. | Mode Maintenance uniquement.
**PowerShell** | Non pris en charge pour le moment. | Pris en charge | Pris en charge


## <a name="support-for-datacenter-management-servers"></a>Prise en charge des serveurs de gestion du centre de données

### <a name="virtualization-management-entities"></a>Entités de gestion de la virtualisation

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour
**Hyper-V (avec VMM)** | SCVMM 2016 et SCVMM 2012 R2

  >[!Note]
  > Les clouds SCVMM 2016 qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge.

### <a name="host-servers"></a>Serveurs hôtes

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement) 
**Hyper-V (sans VMM)** | Windows Server 2016, Windows Server 2012 R2 avec les dernières mises à jour
**Hyper-V avec VMM** | Windows Server 2016, Windows Server 2012 R2 avec les dernières mises à jour<br/><br/> Les hôtes Windows Server 2016 doivent être gérés par SCVMM 2016

  >[!Note]
  >Les sites Hyper-V qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge.

## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée

Les machines virtuelles qui sont protégées doivent répondre aux [conditions requises pour Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) lors de la réplication vers Azure.
Le tableau ci-dessous récapitule la prise en charge des systèmes d’exploitation répliqués dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery. Cette prise en charge est **applicable pour toutes les charges de travail** en cours d’exécution sur le système d’exploitation mentionné.

 **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | ---
Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx) | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!Note]
>**Prise en charge du stockage pour les versions Linux** Système de fichiers (EXT3, ETX4, ReiserFS, XFS) Logiciel multichemin : Device Mapper (multichemin)) Gestionnaire de volumes : (LVM2) Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont **pas** pris en charge.
>Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network"></a>Prise en charge du réseau
Les tableaux suivants récapitulent la prise en charge de la configuration réseau dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-network-configuration"></a>Configuration du réseau hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Association de cartes réseau | Oui<br/><br/>Non pris en charge dans des machines physiques| Oui | Oui
VLAN | Oui | Oui | Oui
IPv4 | Oui | Oui | Oui
IPv6 | Non | Non | Non

### <a name="guest-vm-network-configuration"></a>Configuration du réseau de machines virtuelles invitées

**Configuration** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Association de cartes réseau | Non | Non | Non
IPv4 | Oui | Oui | Oui
IPv6 | Non | Non | Non
Adresse IP statique (Windows) | Oui | Oui | Oui
Adresse IP statique (Linux) | Non | Non | Non
Plusieurs cartes réseau | Oui | Oui | Oui

### <a name="failed-over-azure-vm-network-configuration"></a>Configuration de réseau des machines virtuelles Azure basculées

**Mise en réseau Azure** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
ExpressRoute | Oui | Oui | Oui
ILB | Oui | Oui | Oui
ELB | Oui | Oui | Oui
Traffic Manager | Oui | Oui | Oui
Plusieurs cartes réseau | Oui | Oui | Oui
Adresse IP réservée | Oui | Oui | Oui
IPv4 | Oui | Oui | Oui
Conserver l’adresse IP source | Oui | Oui | Oui


## <a name="support-for-storage"></a>Prise en charge du stockage
Les tableaux suivants récapitulent la prise en charge de la configuration de stockage dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-storage-configuration"></a>Configuration du stockage hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques | N/D | N/D
SMB 3.0 | N/D | Oui | Oui
SAN (ISCSI) | Oui | Oui | Oui
Chemins d’accès multiples (MPIO)<br></br>Testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | Oui pour VMware<br/><br/> | Oui | Oui

### <a name="guest-physical-server-storage-configuration"></a>Configuration du stockage sur serveur physique/invité

**Configuration** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
VMDK | Oui | N/D | N/D
VHD/VHDX | N/D | Oui | Oui
Machine virtuelle de 2e génération | N/D | Oui | Oui
EFI/UEFI| Non | Oui | Oui
Disque de cluster partagé | Oui pour VMware<br/><br/> NA pour les serveurs physiques | Non | Non
Disque chiffré | Non | Non | Non
NFS | Non | N/D | N/D
SMB 3.0 | Non | Non | Non
RDM | Oui<br/><br/> NA pour les serveurs physiques | N/D | N/D
Disque > 1 To | Non | Non | Non
Volume avec disque à bandes > 1 To<br/><br/> Gestion des volumes logiques | Oui | Oui | Oui
Espaces de stockage | Non | Oui | Oui
Ajout/suppression de disque à chaud | Non | Non | Non
Exclure le disque | Oui | Oui | Oui
Chemins d’accès multiples (MPIO) | N/D | Oui | Oui

**Stockage Azure** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
LRS | Oui | Oui | Oui
GRS | Oui | Oui | Oui
Stockage froid | Non | Non | Non
Stockage chaud| Non | Non | Non
Chiffrement au repos (SSE)| Oui | Oui | Oui
Stockage Premium | Oui | Non | Non
Service Import/Export | Non | Non | Non


## <a name="support-for-azure-compute-configuration"></a>Prise en charge de la configuration de calcul Azure

**Fonctionnalité de calcul** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Groupes à haute disponibilité | Non | Non | Non
CONCENTRATEUR | Oui | Oui | Oui

## <a name="failed-over-azure-vm-requirements"></a>Exigences de machine virtuelle Azure basculée

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Les machines virtuelles locales à répliquer doivent répondent à la configuration requise d’Azure ci-dessous lors de la réplication vers Azure.

**Entité** | **Configuration requise** | **Détails**
--- | --- | ---
**Système d’exploitation invité** | Pour une réplication de Hyper-V sur Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de VMware et de serveur physique, vérifiez les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Architecture du système d’exploitation invité** | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque du système d’exploitation** | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques du système d’exploitation** | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques de données** | 16 ou moins (la valeur maximale est fonction de la taille de la machine virtuelle créée. 16 = XL). | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque dur virtuel de données** | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Adaptateurs réseau** | Prise en charge de plusieurs adaptateurs réseau. |
**Disque dur virtuel partagé** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque FC** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Format de disque dur** | Disque dur virtuel (VHD)  <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
**BitLocker** | Non pris en charge | Bitlocker doit être désactivé préalablement à la protection d’une machine virtuelle.
**Nom de la machine virtuelle** | Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery
**Type de machine virtuelle** | Génération 1<br/><br/> Génération 2 - Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et d’une taille inférieure à 300 Go sont prises en charge.<br></br>Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Prise en charge des actions de coffre Recovery Services

**Action** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non | Non


## <a name="support-for-provider-and-agent"></a>Prise en charge du fournisseur et de l’agent

**Nom** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Installé sur des serveurs VMM locaux ou des serveurs Hyper-V, si aucun serveur VMM n’existe | 5.1.19 ([disponible sur le portail](http://aka.ms/downloaddra)) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Installation unifiée d’Azure Site Recovery (VMware vers Azure)** | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/> Installé sur des serveurs VMware locaux | 9.3.4246.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer  | N/A (disponible sur le portail) | N/D
**Agent Microsoft Azure Recovery Services (MARS)** | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V locaux (avec ou sans serveur VMM) | Dernier agent ([disponible sur le portail](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Étapes suivantes
[Préparer le déploiement](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


