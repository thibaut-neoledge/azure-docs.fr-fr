<properties
	pageTitle="Matrice de prise en charge Azure Site Recovery | Microsoft Azure"
	description="Résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/07/2016"
	ms.author="raynew"/>

# Matrice de prise en charge Azure Site Recovery

Cet article résume les systèmes d’exploitation et composants pris en charge pour les déploiements Azure Site Recovery. Une liste des conditions préalables et des composants pris en charge est disponible pour chaque scénario de déploiement, dans chaque article de déploiement, mais cette rubrique résume l’ensemble.

## Systèmes d’exploitation pris en charge pour les serveurs de virtualisation


**Réplication** | **Ce qui est répliqué** | **Réplication vers** | **Système d’exploitation hôte**
---|---|---|--- 
**Hôtes Hyper-V** | Toute charge de travail | Microsoft Azure | Windows Server 2012 R2 avec les dernières mises à jour
**Hôtes Hyper-V dans les clouds VMM** | Toute charge de travail | Microsoft Azure | Windows Server 2012 R2 avec les dernières mises à jour
**Hôtes Hyper-V dans les clouds VMM** | Toute charge de travail | Site VMM secondaire | Au minimum Windows Server 2012 avec les dernières mises à jour
**Hôtes VMware/vCenter** | Toute charge de travail | Microsoft Azure | vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement) <br/><br/> vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour
**Hôtes VMware/vCenter** | Toute charge de travail | Site VMware secondaire | vCenter 5.5 ou 6.0 (prise en charge des fonctionnalités 5.5 uniquement) <br/><br/> vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour

## Prise en charge requise pour les machines répliquées

**Réplication** | **Ce qui est répliqué** | **Réplication vers** | **Système d’exploitation hôte**
---|---|---|--- 
**Machines virtuelles Hyper-V** | Toute charge de travail | Microsoft Azure | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles Hyper-V dans le cloud VMM** | Toute charge de travail | Microsoft Azure | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles Hyper-V dans les clouds VMM** | Toute charge de travail | Site VMM secondaire | N’importe quel système d’exploitation invité [pris en charge par Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**Machines virtuelles VMware** | Toute charge de travail en cours d’exécution sur une machine virtuelle Windows | Microsoft Azure | Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles VMware** | Toute charge de travail en cours d’exécution sur une machine virtuelle Linux | Microsoft Azure | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Stockage requis : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie)) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles VMware** | Toute charge de travail en cours d’exécution sur une machine virtuelle Windows | Site VMware secondaire | Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1
**Machines virtuelles VMware** | Toute charge de travail en cours d’exécution sur une machine virtuelle Linux | Site VMware secondaire | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Stockage requis : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie)) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.
**Serveurs physiques** | Toute charge de travail en cours d’exécution sous Windows | Microsoft Azure | Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 avec au moins SP1
**Serveurs physiques** | Toute charge de travail en cours d’exécution sous Linux | Microsoft Azure | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Stockage requis : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie)) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.
**Serveurs physiques** | Toute charge de travail en cours d’exécution sous Windows | Site secondaire | Windows Server 2012 R2 64 bits, Windows Server 2012, Windows Server 2008 avec au moins SP1
**Serveurs physiques** | Toute charge de travail en cours d’exécution sous Linux | Site secondaire | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Stockage requis : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie)) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.


## Versions du fournisseur

**Name** | **Description** | **Version la plus récente** | **Support** | **Détails**
---|---|---|---| ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre des serveurs locaux et le site Azure/secondaire <br/><br/> Installé sur des serveurs VMM locaux ou des serveurs Hyper-V, si aucun serveur VMM n’existe | 5\.1.1700 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Installation unifiée d’Azure Site Recovery (VMware vers Azure)** | Coordonne les communications entre les serveurs VMware locaux et Azure <br/><br/> Installé sur des serveurs VMware locaux | 9\.3.4246.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire | N/A (disponible sur le portail) | Installé sur chaque machine virtuelle ou serveur physique VMware que vous souhaitez répliquer. **Agent Microsoft Azure Recovery Services (MARS)** | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V locaux (avec ou sans serveur VMM) | 2\.0.8689.0 (disponible sur le portail) | Cet agent est utilisé par Azure Site Recovery et Azure Backup. [En savoir plus](https://support.microsoft.com/fr-FR/kb/2997692)

## Étapes suivantes

[Préparer le déploiement](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0907_2016-->