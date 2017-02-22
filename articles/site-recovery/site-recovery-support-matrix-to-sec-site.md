---
title: "Matrice de support Azure Site Recovery pour la réplication vers le site secondaire | Microsoft Docs"
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
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>Matrice de support Azure Site Recovery pour la réplication vers un site secondaire appartenant au client

> [!div class="op_single_selector"]
> * [Réplication vers Azure](site-recovery-support-matrix-to-azure.md)
> * [Réplication vers un site secondaire appartenant au client](site-recovery-support-matrix-to-sec-site.md)

Une liste des conditions préalables pour Azure Site Recovery est mentionnée [ici](site-recovery-best-practices.md) et l’article ci-dessous résume les configurations et les composants pris en charge pour Azure Site Recovery lors de la réplication et de la récupération pour un site secondaire appartenant au client.

## <a name="support-for-deployment-options"></a>Prise en charge des options de déploiement

**Déploiement** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
**Portail Azure** | Machines virtuelles VMware locales vers site VMware secondaire.<br/><br/> Téléchargez le guide d’aide](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) le guide d’utilisation d’InMage Scout. Non disponible dans le portail Azure | Non pris en charge | Machines virtuelles Hyper-V locales résidant dans des clouds VMM vers un cloud VMM secondaire<br/><br/> Réplication Hyper-V standard uniquement, SAN non pris en charge
**Portail classique** | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Non pris en charge | Mode Maintenance uniquement.
**PowerShell** | Non pris en charge. | Non pris en charge | Pris en charge



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
**Hyper-V (sans VMM)** | Configuration non prise en charge pour la réplication vers un site secondaire
**Hyper-V avec VMM** | Windows Server 2016, Windows Server 2012 R2 avec les dernières mises à jour<br/><br/> Les hôtes Windows Server 2016 doivent être gérés par SCVMM 2016

## <a name="support-for-replicated-machine-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée
Le tableau ci-dessous récapitule la prise en charge des systèmes d’exploitation dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery. Cette prise en charge est **applicable pour toutes les charges de travail** en cours d’exécution sur le système d’exploitation mentionné.

**Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | N’importe quel système d’exploitation invité [pris en charge par Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>**Prise en charge du stockage pour les versions Linux** Système de fichiers (EXT3, ETX4, ReiserFS, XFS) Logiciel multichemin : Device Mapper (multichemin)) Gestionnaire de volumes : (LVM2) Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont **pas** pris en charge.
>Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network"></a>Prise en charge du réseau
Les tableaux suivants récapitulent la prise en charge de la configuration réseau dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-network-configuration"></a>Configuration du réseau hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Association de cartes réseau | Oui | Oui
VLAN | Oui | Oui
IPv4 | Oui | Oui
IPv6 | Non | Non

### <a name="guest-vm-network-configuration"></a>Configuration du réseau de machines virtuelles invitées

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Association de cartes réseau | Non | Non
IPv4 | Oui | Oui
IPv6 | Non | Non
Adresse IP statique (Windows) | Oui | Oui
Adresse IP statique (Linux) | Oui | Oui
Plusieurs cartes réseau | Oui | Oui


## <a name="support-for-storage"></a>Prise en charge du stockage
Les tableaux suivants récapitulent la prise en charge de la configuration de stockage dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-storage-configuration"></a>Configuration du stockage hôte

**Stockage (hôte)** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
NFS | Oui | N/D
SMB 3.0 | N/D | Oui
SAN (ISCSI) | Oui | Oui
Chemins d’accès multiples (MPIO) | Oui | Oui

### <a name="guest-physical-server-storage-configuration"></a>Configuration du stockage sur serveur physique/invité

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
VMDK | Oui | N/D
VHD/VHDX | N/D | Oui (jusqu’à 16 disques)
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

**Action** | **Serveur VMware/physique** | **Hyper-V (avec VMM)**
--- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non

## <a name="support-for-provider-and-agent"></a>Prise en charge du fournisseur et de l’agent

**Nom** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Installé sur des serveurs VMM locaux ou des serveurs Hyper-V, si aucun serveur VMM n’existe | 5.1.19 ([disponible sur le portail](http://aka.ms/downloaddra)) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et le site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer  | N/A (disponible sur le portail) | N/D


## <a name="next-steps"></a>Étapes suivantes
[Préparer le déploiement](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


