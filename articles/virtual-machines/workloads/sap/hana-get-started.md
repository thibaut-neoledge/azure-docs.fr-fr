---
title: "Guide de démarrage rapide : installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure | Microsoft Docs"
description: "Guide de démarrage rapide pour l’installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: e95d3a57594d55b7d7176d873cde8abfa6e12ecd
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guide de démarrage rapide : installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure
## <a name="introduction"></a>Introduction
Ce guide de démarrage rapide vous aide à configurer un prototype ou un système de démonstration SAP HANA à instance unique sur des machines virtuelles Azure lors de l’installation manuelle de SAP NetWeaver 7.5 et SAP HANA 1.0 SP12. Ce guide ne vise pas à remplacer la documentation SAP, mais s’intéresse davantage au déploiement sur Azure.

>[!Note]
>Veuillez noter que ce guide concerne les déploiements de SAP HANA sur des machines virtuelles Azure. Toutes les considérations relatives au déploiement de SAP HANA dans des grandes instances HANA sont abordées dans des documents spécifiques se trouvant également à l’adresse https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started.
 

Il suppose que vous maîtrisiez certaines bases d’Azure IaaS, notamment :
 * Comment déployer des machines virtuelles ou des réseaux virtuels via le portail Azure ou PowerShell.
 * L’outil en ligne de commande multiplateforme (CLI) Azure, y compris la possibilité d’utiliser des modèles JSON (JavaScript Object Notification).

Ce guide part également du principe que vous connaissez SAP HANA et SAP NetWeaver, et savez comment les installer en local.

Vous devez également connaître et maîtriser les points suivants pour installer et utiliser SAP HANA et des instances d’application SAP sur Azure :

Planification du déploiement SAP sur Azure, y compris la planification du réseau virtuel et l’utilisation de Stockage Azure. Consultez [SAP NetWeaver sur machines virtuelles Azure – Guide de planification et d’implémentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).

Pour en savoir plus sur les principes de déploiement et les méthodes de déploiement de machines virtuelles dans Azure, consultez [Déploiement de machines virtuelles Azure pour SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).

Pour en savoir plus sur la haute disponibilité des instances ASCS, SCS et ERS SAP NetWeaver sur Azure, reportez-vous au guide [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).

Le guide [Créer une configuration SAP NetWeaver multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid) explique en détail comment améliorer l’efficacité en utilisant une installation multi-SID d’instances ASCS/SCS sur Azure.

Les principes de l’exécution de SAP NetWeaver sur des machines virtuelles Linux dans Azure sont expliqués dans l’article [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Nous vous recommandons de lire ce guide, car il détaille quelques-uns des paramètres spécifiques à Linux dans les machines virtuelles Azure et explique comment attacher des disques de stockage Azure à des machines virtuelles Linux.

À l’heure actuelle, les machines virtuelles Azure sont certifiées par SAP pour les configurations de montée en puissance SAP HANA uniquement. Les configurations incluant une augmentation de la taille des instances avec une charge de travail SAP HANA ne sont pas encore prises en charge. Pour la haute disponibilité de SAP HANA dans les configurations de montée en puissance, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).


Vous trouverez des recommandations et des solutions pour sauvegarder des bases de données SAP HANA sur des machines virtuelles dans les guides suivants :

* [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Sauvegarde SAP HANA sur Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

L’utilisation de la bibliothèque d’appliances cloud SAP pour déployer S/4HANA ou BW/4HANA est décrite dans l’article [Déploiement de SAP S/4HANA ou BW/4HANA sur Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

SAP documente les systèmes d’exploitation pris en charge par SAP HANA dans la [note de support SAP #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (SAP HANA : Systèmes d’exploitation pris en charge). Seul un sous-ensemble de ces systèmes d’exploitation est pris en charge sur les machines virtuelles Azure. Les systèmes d’exploitation suivants sont pris en charge pour déployer SAP HANA sur Azure : 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Vous trouverez ci-dessous d’autres documents de SAP concernant SAP HANA et les différents systèmes d’exploitation Linux :

* [Note de support SAP #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Logiciel SAP sur Linux : informations générales)
* [Note de support SAP #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Instructions SAP HANA pour l’installation du système d’exploitation SLES)
* [Note de support SAP #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Paramètres SAP HANA DB de système d’exploitation recommandés pour SLES 12 for SAP Applications)
* [Note de support SAP #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (SUSE Linux Enterprise Server 12 : notes d’installation)
* [Note de support SAP #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Solutions Linux UUID)
* [Note de support SAP #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879) (Instructions SAP HANA pour les systèmes d’exploitation Red Hat Enterprise Linux (RHEL))
* [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E) (SAP HANA DB : Paramètres de système d’exploitation recommandés pour RHEL 7)

Pensez également à consulter ces deux notes SAP en lien avec la surveillance SAP dans Azure :

* Note SAP sur la « surveillance améliorée » SAP avec des machines virtuelles Linux sur Azure : [Note SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E).
* Note SAP contenant des informations relatives à SAPOSCOL sur Linux : [Note SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E).
* Métriques de surveillance clés pour SAP sur Microsoft Azure : [Note SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E).


Les types de machines virtuelles Azure et les scénarios de charge de travail pris en charge par SAP pour SAP HANA sont documentés sur la [plateforme IaaS certifiée SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Les types de machines virtuelles Azure pouvant être utilisées et certifiées par SAP pour SAP NetWeaver ou la couche d’application S/4HANA sont documentées par SAP dans [Note de support SAP 1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Applications SAP sur Azure : produits et types de machines virtuelles pris en charge).

>[!Note]
>SAP-Linux-Azure est pris en charge uniquement sur Azure Resource Manager (ARM) ; le modèle de déploiement classique (ASM) n’est pas compatible. 


Ce guide explique comment installer manuellement SAP HANA sur des machines virtuelles Azure de deux manières :

* En utilisant SAP Software Provisioning Manager (SWPM) dans le cadre d’une installation NetWeaver distribuée à l’étape d’installation de l’instance de base de données.
* En utilisant l’outil de gestion du cycle de vie HANA hdblcm, puis en installant NetWeaver.

Vous pouvez également utiliser SWPM et installer tous les composants (SAP HANA, serveur d’applications SAP, instance ASCS) sur une seule machine virtuelle, comme décrit [ici](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Même si cette option n’est pas décrite dans ce guide, les éléments à prendre en considération sont les mêmes.

Avant de commencer une installation, veillez à lire la section « Préparation des machines virtuelles Azure pour une installation manuelle de SAP HANA », qui suit immédiatement les deux listes de contrôle pour l’installation de SAP HANA. Cela vous aidera à prévenir plusieurs erreurs de base qui peuvent survenir lorsque vous utilisez seulement une configuration de machine virtuelle Azure par défaut.

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>Liste de contrôle pour l’installation de SAP HANA avec SAP SWPM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique lorsque vous utilisez SAP SWMP pour réaliser une installation distribuée de SAP NetWeaver 7.5. Chaque élément est expliqué plus en détail dans les captures d’écran qui illustrent ce guide.

* Créer un réseau virtuel Azure qui inclut les deux machines virtuelles de test.
* Déployer deux machines virtuelles Azure avec le système d’exploitation (dans notre exemple, il s’agit de SLES/SLES-for-SAP Applications 12 SP1) selon le modèle Azure Resource Manager.
* Attacher deux disques de stockage Azure Standard ou Premium à la machine virtuelle du serveur d’applications (par exemple, des disques de 75 Go ou 500 Go).
* Attacher des disques de stockage Premium à la machine virtuelle du serveur HANA DB. Pour plus de détails, consultez la section Configuration des disques.
* Selon les exigences de taille ou de débit, attacher plusieurs disques et créer des volumes agrégés par bandes en utilisant la gestion de volumes logiques (LVM) ou un utilitaire d’administration de périphériques multiples (mdadm) au niveau du système d’exploitation à l’intérieur de la machine virtuelle.
* Créer des systèmes de fichiers XFS sur les disques/volumes logiques attachés.
* Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour stocker tous les logiciels SAP et l’autre pour, par exemple, le répertoire /sapmnt et éventuellement les sauvegardes. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour empêcher la saturation du système de fichiers racine, peu volumineux sur les machines virtuelles Linux Azure.
* Entrer les adresses IP locales des machines virtuelles de test dans /etc/hosts.
* Entrer le paramètre nofail dans /etc/fstab.
* Définir les paramètres de noyau Linux en fonction des notes SAP associées à la version du système d’exploitation Linux et de HANA que vous utilisez. Pour plus d’informations, voir la section « Paramètres de noyau ».
* Ajouter un espace d’échange.
* Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
* Télécharger les logiciels SAP à partir du SAP Service Marketplace.
* Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
* Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
* Avec l’outil SWPM, installer l’instance de base de données, y compris HANA, sur la machine virtuelle du serveur de base de données.
* Installer le serveur d’applications principal (PAS) sur la machine virtuelle du serveur d’applications.
* Démarrer la console de gestion (MC) SAP et se connecter par exemple avec l’interface utilisateur graphique SAP/HANA Studio.

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>Liste de contrôle pour l’installation de SAP HANA avec hdblcm
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique à des fins de démonstration ou de création d’un prototype lorsque vous utilisez SAP hdblcm pour réaliser une installation distribuée de SAP NetWeaver 7.5. Chaque élément est expliqué plus en détail dans les captures d’écran qui illustrent ce guide.

* Créer un réseau virtuel Azure qui inclut les deux machines virtuelles de test.
* Déployer deux machines virtuelles Azure avec le système d’exploitation (dans notre exemple, il s’agit de SLES/SLES-for-SAP Applications 12 SP1) selon le modèle Azure Resource Manager.
* Attacher deux disques de stockage Azure Standard ou Premium à la machine virtuelle du serveur d’applications (par exemple, des disques de 75 Go ou 500 Go).
* Attacher des disques de stockage Premium à la machine virtuelle du serveur HANA DB. Pour plus de détails, consultez la section Configuration des disques.
* Selon les exigences de taille ou de débit, attacher plusieurs disques et créer des volumes agrégés par bandes en utilisant la gestion de volumes logiques (LVM) ou un utilitaire d’administration de périphériques multiples (mdadm) au niveau du système d’exploitation à l’intérieur de la machine virtuelle.
* Créer des systèmes de fichiers XFS sur les disques/volumes logiques attachés.
* Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour stocker tous les logiciels SAP et l’autre pour, par exemple, le répertoire /sapmnt et éventuellement les sauvegardes. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour empêcher la saturation du système de fichiers racine, peu volumineux sur les machines virtuelles Linux Azure.
* Entrer les adresses IP locales des machines virtuelles de test dans /etc/hosts.
* Entrer le paramètre nofail dans /etc/fstab.
* Définir les paramètres de noyau en fonction des notes SAP associées à la version du système d’exploitation Linux et de HANA que vous utilisez. Pour plus d’informations, voir la section « Paramètres de noyau ».
* Ajouter un espace d’échange.
* Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
* Télécharger les logiciels SAP à partir de SAP Service Marketplace.
* Créer un groupe, « sapsys », avec l’ID de groupe 1001 sur la machine virtuelle du serveur de base de données HANA.
* Installer SAP HANA sur la machine virtuelle du serveur de base de données à l’aide du gestionnaire de cycle de vie des bases de données HANA (HDBLCM).
* Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
* Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
* Avec l’outil SWPM, installer l’instance de base de données, y compris HANA, sur la machine virtuelle du serveur de base de données HANA.
* Installer le serveur d’applications principal (PAS) sur la machine virtuelle du serveur d’applications.
* Démarrer la console de gestion SAP et se connecter via l’interface utilisateur graphique SAP/HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Préparation des machines virtuelles Azure pour une installation manuelle de SAP HANA
Cette section couvre les sujets suivants :

* Mises à jour de système d’exploitation
* Configuration des disques
* Paramètres de noyau
* Systèmes de fichiers
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>Mises à jour de système d’exploitation
Les distributeurs de systèmes d’exploitation Linux fournissent des mises à jour et correctifs pour le système d’exploitation, ce qui contribue à maintenir sa sécurité et son bon fonctionnement. Il est donc judicieux de vérifier s’il existe des mises à jour disponibles avant d’installer des logiciels supplémentaires.
Bien souvent, un appel au support technique peut être évité si le système est au niveau de correctif actuel. Vérifiez également que vous utilisez :
* SUSE Linux Enterprise Server pour les applications SAP
* Red Hat Enterprise Linux pour les applications SAP ou pour SAP HANA 

Si vous ne l’avez pas déjà fait, enregistrez le déploiement du système d’exploitation dans votre abonnement Linux auprès du fournisseur de Linux. Veuillez noter que pour SUSE, il existe également des images de système d’exploitation SUSE pour les applications SAP qui incluent déjà des services et qui sont enregistrées automatiquement.

Par exemple, avec SUSE Linux, vérifiez simplement les correctifs disponibles avec :

 `sudo zypper list-patches`

Selon le type de défaut, les correctifs sont classés par catégorie et par gravité.

Parmi les valeurs couramment utilisées pour la catégorie : sécurité, recommandé, facultatif, fonctionnalité, document ou yast.

Parmi les valeurs couramment utilisées pour la gravité : critique, important, modéré, faible ou non spécifié.

zypper recherche uniquement les mises à jour nécessaires pour vos packages installés.

Par conséquent, un exemple de commande peut être :

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Si vous ajoutez le paramètre *--dry-run*, vous pouvez tester la mise à jour, mais elle n’actualise pas réellement le système.


### <a name="disk-setup"></a>Configuration des disques
La taille du système de fichiers racine d’une machine virtuelle Linux sur Azure est limitée. Par conséquent, il est nécessaire d’attacher un espace disque supplémentaire à une machine virtuelle Azure pour l’exécution de SAP. Pour les machines virtuelles Azure de serveur d’applications SAP, l’utilisation de disques de stockage Azure Standard peut être tolérée. Toutefois, pour les machines virtuelles Azure SGBD SAP HANA, l’utilisation de disques de stockage Azure Premium dans les implémentations de production et hors production est obligatoire.

En fonction des [exigences de stockage TDI SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), la configuration du stockage Azure Premium suivante est suggérée : 

| Référence de la machine virtuelle | RAM |  /hana/data et /hana/log <br /> agrégés avec LVM ou MDAADM | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 Go | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Dans la configuration de disque suggérée, le volume de données et le volume de journal HANA seraient placés sur le même ensemble de disques de stockage Azure Premium qui sont agrégés avec LVM ou MDADM. Il n’est pas nécessaire de définir un niveau de redondance RAID, car le stockage Azure Premium conserve trois images des disques à des fins de redondance. Vous pouvez opter pour différentes configurations de disques. Toutefois, veuillez consulter les documents [SAP HANA TDI storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Exigences de stockage TDI SAP HANA) et [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guide d’installation et de mise à jour du serveur SAP HANA) pour vérifier que vous avez configuré suffisamment de stockage. Prenez également en compte les différents volumes de débit VHD des divers disques de stockage Azure Premium, comme expliqué dans [Stockage Premium hautes performances et disques gérés pour machines virtuelles](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

En fonction de la demande, vous pouvez ajouter des disques de stockage Premium aux machines virtuelles HANA SGBD, afin de stocker les sauvegardes du journal des transactions ou de bases de données.

Pour plus d’informations sur les deux principaux outils permettant de configurer l’agrégation par bandes, consultez les articles suivants :

* [Configuration d’un RAID logiciel sur Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurer LVM sur une machine virtuelle Linux dans Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pour plus d’informations sur comment attacher des disques à une machine virtuelle Azure exécutant Linux comme système d’exploitation invité, consultez [Ajouter un disque à une machine virtuelle Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Le stockage Azure Premium permet de définir les modes de mise en cache des disques. Pour l’ensemble de disques agrégés contenant /hana/data et /hana/log, la mise en cache des disques doit être désactivée. Pour les autres volumes (disques), le mode de mise en cache doit être défini sur READONLY.

Pour plus d’informations, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../../../storage/storage-premium-storage.md) .

Pour rechercher des exemples de modèles JSON pour la création de machines virtuelles, accédez à [Modèles de démarrage rapide Microsoft Azure](https://github.com/Azure/azure-quickstart-templates).
Le modèle « vm-simple-sles » illustre un modèle de base comprenant une section de stockage avec un disque de données de 100 Go supplémentaire. Ce modèle peut être utilisé comme base. Il est probable que vous deviez adapter le modèle à votre configuration.

>[!Note]
>N’oubliez pas qu’il est essentiel d’attacher le disque de stockage Azure à l’aide de l’UUID, comme expliqué dans [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


Dans l’environnement de test, deux disques de stockage standard Azure ont été attachés à la machine virtuelle du serveur applications SAP, comme illustré dans la capture d’écran suivante. Un des disques était utilisé pour stocker tous les logiciels SAP à installer (c’est-à-dire NetWeaver 7.5, GUI SAP, SAP HANA, etc.). Le second disque permettait de garantir un espace disponible suffisant pour les besoins supplémentaires (par exemple, pour les données de sauvegarde et de test) et le répertoire /sapmnt (c’est-à-dire les profils SAP) à partager entre toutes les machines virtuelles appartenant au même au même paysage SAP.

![Fenêtre des disques du serveur d’applications SAP HANA montrant deux disques de données et leur taille](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Paramètres de noyau
SAP HANA nécessite des paramètres de noyau Linux spécifiques, qui ne font pas partie des images de galerie Azure standard et doivent être définis manuellement. En fonction de SUSE ou de Red Hat, les paramètres peuvent différer. Les notes SAP mentionnées précédemment donnent des informations sur ces paramètres. Dans les captures d’écran présentées, SUSE Linux 12 SP1 a été utilisé. 

SLES-for-SAP Applications 12 GA et SP1 offrent un nouvel outil qui remplace l’ancien utilitaire sapconf, appelé tuned-adm. Un profil SAP HANA spécial est disponible pour cet outil. Pour paramétrer le système pour SAP HANA, saisissez simplement en tant qu’utilisateur racine : 'tuned-adm profile sap-hana'

Pour plus d’informations sur tuned-adm, consultez la documentation de SUSE sur :

* [Documentation de SLES-for-SAP Applications 12 SP1 sur tuned-adm profile sap-hana.](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

Dans la capture d’écran suivante, vous pouvez voir comment tuned-adm a modifié les valeurs transparent_hugepage et numa_balancing en fonction des paramètres SAP HANA requis.

![L’outil tuned-adm modifie les valeurs en fonction des paramètres SAP HANA requis](./media/hana-get-started/image005.jpg)

Pour conserver les paramètres de noyau SAP HANA, utilisez grub2 sur SLES 12. Pour plus d’informations sur grub2, accédez à la [section « Configuration File Structure » (Structure du fichier de configuration) de la documentation SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

La capture d’écran suivante montre comment les paramètres de noyau ont été modifiés dans le fichier de configuration, puis compilés à l’aide de grub2-mkconfig.

![Paramètres de noyau modifiés dans le fichier de configuration et compilés à l’aide de grub2-mkconfig](./media/hana-get-started/image006.jpg)

Vous pouvez également modifier les paramètres à l’aide de YaST et des réglages de **Chargeur de démarrage** > **Paramètres du kernel**.

![Onglet Paramètres du kernel dans le chargeur de démarrage YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systèmes de fichiers
La capture d’écran suivante montre les deux systèmes de fichiers qui ont été créés sur la machine virtuelle du serveur d’applications SAP, sur les deux disques de stockage standard Azure. Les deux systèmes de fichiers présentent le type XFS et sont montés sur /sapdata et /sapsoftware.

Vous n’êtes pas obligé de structurer vos systèmes de fichiers de cette façon. D’autres options sont disponibles pour structurer l’espace disque. Le plus important est d’empêcher le système de fichiers racine de manquer d’espace libre.

![Deux systèmes de fichiers créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image008.jpg)

En ce qui concerne la machine virtuelle SAP HANA DB, il est important de garder à l’esprit que lors d’une installation de base de données, si vous utilisez SAPinst (SWPM) et la simple option d’installation « classique », tous les éléments sont installés sous /hana et /usr/sap. Le paramètre par défaut pour la sauvegarde des journaux SAP HANA se trouve sous /usr/sap. Là encore, comme il est important d’empêcher le système de fichiers racine de manquer d’espace de stockage, assurez-vous qu’il y a suffisamment d’espace libre sous /hana et /usr/sap avant d’installer SAP HANA à l’aide de SWPM.

Pour obtenir une description de la disposition de système de fichiers standard de SAP HANA, consultez le document [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guide d’installation et de mise à jour du serveur SAP HANA).
![Systèmes de fichiers supplémentaires créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image009.jpg)

Lors de l’installation de SAP NetWeaver sur une image de galerie Azure SLES/SLES-for-SAP Applications 12 standard, un message s’affiche pour indiquer l’absence d’espace d’échange. Pour faire disparaître ce message, vous pouvez ajouter manuellement un fichier d’échange à l’aide des options dd, mkswap et swapon. Pour en savoir plus, recherchez « Adding a Swap File Manually » (Ajout manuel d’un fichier d’échange) dans la [section « Using the YaST Partitioner » (Utilisation du partitionneur YaST) de la documentation SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Vous pouvez également configurer l’espace d’échange à l’aide de l’agent de machine virtuelle Linux. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![Message contextuel indiquant que l’espace d’échange est insuffisant](./media/hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
Avant de commencer l’installation de SAP, il est également important d’inclure les noms d’hôtes et les adresses IP des machines virtuelles SAP dans le fichier /etc/hosts. Déployez toutes les machines virtuelles SAP dans un même réseau virtuel Azure, puis utilisez les adresses IP internes.

![Noms d’hôte et adresses IP des machines virtuelles SAP répertoriés dans le fichier/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

Pendant la phase de test, nous avons constaté qu’il était judicieux d’ajouter le paramètre nofail à fstab. En effet, en cas de problème avec les disques, la machine virtuelle continue à s’afficher et ne reste pas bloquée dans le processus de démarrage. Cependant, faites bien attention, car dans ce scénario, l’espace disque supplémentaire n’est peut-être pas disponible et les processus peuvent saturer le système de fichiers racine. Si /hana est manquant, SAP HANA ne démarrera pas.

![Ajout du paramètre nofail à fstab](./media/hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Installation du bureau graphique Gnome sur SLES 12/SLES-for-SAP Applications 12
Cette section couvre les sujets suivants :

* Installation du bureau Gnome et de xrdp sur SLES 12/SLES-for-SAP Applications 12
* Exécution de la console de gestion SAP basée sur Java avec Firefox sur SLES 12/SLES-for-SAP Applications 12

Vous pouvez utiliser d’autres solutions, telles que Xterminal ou VNC, mais au mois de septembre 2016, ce guide décrit uniquement xrdp.

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installation du bureau Gnome et de xrdp sur SLES 12/SLES-for-SAP Applications 12
Si vous êtes habitué à Microsoft Windows, vous pouvez facilement utiliser un bureau graphique directement dans les machines virtuelles Linux de SAP pour exécuter Firefox, Sapinst, l’interface utilisateur graphique SAP, la console de gestion SAP ou HANA Studio et vous connecter avec RDP à partir d’un ordinateur Windows. Bien que cette procédure ne soit pas forcément appropriée pour un serveur de base de données de production, elle convient tout à fait dans le cadre d’un environnement de prototype/démonstration pur. Voici comment faire pour installer le bureau Gnome sur une machine virtuelle Azure SLES 12/SLES-for-SAP Applications 12 :

Installez le bureau Gnome en entrant la commande suivante (dans une fenêtre PuTTY, par exemple) :

`zypper in -t pattern gnome-basic`

Installez xrdp pour autoriser l’établissement d’une connexion à la machine virtuelle via RDP :

`zypper in xrdp`

Modifiez /etc/sysconfig/windowmanager et définissez Gnome comme gestionnaire de fenêtrage par défaut :

`DEFAULT_WM="gnome"`

Exécutez la commande chkconfig pour vous assurer que xrdp démarre automatiquement après un redémarrage :

`chkconfig -level 3 xrdp on`

En cas de problème avec la connexion RDP, essayez de redémarrer (éventuellement à partir d’une fenêtre PuTTY) :

`/etc/xrdp/xrdp.sh restart`

Si le redémarrage de xrdp mentionné précédemment ne fonctionne pas, vérifiez si un fichier .pid est présent et supprimez-le, le cas échéant :

`check /var/run` et recherchez `xrdp.pid`.

Supprimez ce fichier et essayez de redémarrer à nouveau.

### <a name="sap-mc"></a>Console de gestion SAP
Une fois le bureau Gnome installé, une erreur peut s’afficher lors du démarrage de la console de gestion SAP graphique basée sur Java à partir de Firefox sur une machine virtuelle Azure SLES 12/SLES-for-SAP Applications 12 en raison de l’absence du plug-in de navigateur Java.

L’URL de démarrage de la console de gestion SAP est <server>:5<instance_number>13.

Pour plus d’informations, consultez [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm) (Démarrage de la console de gestion web SAP).

La capture d’écran suivante illustre le message d’erreur qui s’affiche lorsque le plug-in de navigateur Java est manquant.

![Message d’erreur indiquant que le plug-in de navigateur Java est manquant](./media/hana-get-started/image013.jpg)

Pour résoudre le problème, une solution consiste simplement à installer le plug-in manquant à l’aide de YaST, comme illustré dans la capture d’écran suivante.

![Utilisation de YaST pour installer le plug-in manquant](./media/hana-get-started/image014.jpg)

Lorsque vous entrez à nouveau l’URL de la console de gestion SAP, une boîte de dialogue vous invitant à activer le plug-in s’affiche.

![Boîte de dialogue demandant l’activation du plug-in](./media/hana-get-started/image015.jpg)

Un autre problème pouvant survenir est un message d’erreur concernant un fichier manquant, à savoir javafx.properties. Cela est lié à l’exigence d’Oracle Java 1.8 pour la version 7.4 de l’interface utilisateur graphique de SAP. [Voir la note SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424) Ni la version IBM Java ni le package openjdk fourni avec SLES/SLES-for-SAP Applications 12 ne comprend le javafx nécessaire. La solution consiste à télécharger et installer Java SE8 auprès d’Oracle.

Vous trouverez un article qui évoque un problème similaire sur openSUSE avec openjdk sur [Version Java de l’interface utilisateur graphique SAP 7.4 pour openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>Installation manuelle de SAP HANA avec SWPM dans le cadre d’une installation de NetWeaver 7.5
La série de captures d’écran de cette section illustre les principales étapes d’installation de SAP NetWeaver 7.5 et SAP HANA SP12 avec SWPM (SAPinst). Lors de l’installation de NW 7.5, SWPM permet également d’installer la base de données HANA en tant qu’instance unique.

Dans l’environnement de test, nous avons installé une seul serveur application ABAP (Advanced Business Application Programming). Comme illustré dans la capture d’écran suivante, nous avons utilisé l’option « Distributed System » (Système distribué) pour installer l’instance ASCS et l’instance de serveur d’applications principal sur une machine virtuelle Azure, et SAP HANA en tant que système de base de données sur une autre machine virtuelle Azure.

![Installation de l’instance ASCS et de l’instance de serveur d’applications principal à l’aide de l’option « Distributed System » (Système distribué)](./media/hana-get-started/image012.jpg)

Une fois que l’instance ASCS est installée sur la machine virtuelle du serveur d’applications et signalée en vert dans la console de gestion SAP, le répertoire /sapmnt (qui inclut notamment le répertoire des profils SAP) doit être partagé avec la machine virtuelle du serveur de base de données SAP HANA. L’étape d’installation de la base de données requiert l’accès à ces informations. Pour fournir cet accès, le meilleur moyen consiste à utiliser NFS, qui peut être configuré à l’aide de YaST.

![Console de gestion SAP montrant l’instance ASCS installée sur la machine virtuelle du serveur d’applications et signalée en vert](./media/hana-get-started/image016.jpg)

Sur la machine virtuelle du serveur d’applications, le répertoire /sapmnt doit être partagé via NFS à l’aide des options **rw** et **no_root_squash**. Les options par défaut sont « ro » et « root_squash », qui peuvent provoquer des problèmes lors de l’installation de l’instance de base de données.

![Partage du répertoire /sapmnt via NFS à l’aide des options « rw » et « no_root_squash »](./media/hana-get-started/image017b.jpg)

Comme la capture d’écran suivante le montre, le partage du répertoire /sapmnt de la machine virtuelle du serveur d’applications doit être configuré sur la machine virtuelle du serveur de base de donnée SAP HANA via **NFS Client** (à l’aide de YaST).

![Partage /sapmnt configuré à l’aide de « NFS Client »](./media/hana-get-started/image018b.jpg)

Pour effectuer une installation distribuée de NetWeaver 7.5 **Instance de base de données**, comme illustré dans la capture d’écran suivante, connectez-vous à la machine virtuelle du serveur de base de données SAP HANA et démarrez SWPM.

![Installation d’une instance de base de données en se connectant à la machine virtuelle du serveur de base de données SAP HANA et en démarrant SWPM](./media/hana-get-started/image019.jpg)

Après avoir sélectionné l’option d’installation « Typical » (Par défaut) et le chemin d’accès au support d’installation, entrez un identificateur de sécurité (SID) de base de données, le nom d’hôte, le numéro d’instance et le mot de passe de l’administrateur système de la base de données.

![Page de connexion de l’administrateur système de la base de données SAP HANA](./media/hana-get-started/image035b.jpg)

Entrez le mot de passe pour le schéma DBACOCKPIT.

![Zone de saisie du mot de passe pour le schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Entrez une question pour le mot de passe du schéma SAPABAP1.

![Entrer une question pour le mot de passe du schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois la tâche terminée, une coche verte est affichée en regard de chaque phase du processus d’installation de la base de données. Une fenêtre de message doit afficher un message indiquant « Execution of ... Database Instance has completed. » (L’exécution de... Instance de base de données est terminée.).

![Fenêtre de la tâche terminée avec un message de confirmation](./media/hana-get-started/image023.jpg)

Après l’installation, la console de gestion SAP doit également signaler l’instance de base de données en vert et afficher la liste complète des processus SAP HANA (hdbindexserver, hdbcompileserver, etc.).

![Fenêtre de la console de gestion SAP montrant la liste des processus SAP HANA](./media/hana-get-started/image024.jpg)

La capture d’écran suivante montre les éléments de la structure de fichiers sous /hana/shared que SWPM a créés lors de l’installation de SAP HANA. Étant donné qu’il n’y avait aucun moyen de spécifier un autre chemin d’accès, il est important de monter un espace disque supplémentaire sous /hana avant d’installer SAP HANA à l’aide de SWPM pour empêcher le système de fichiers racine de manquer d’espace libre.

![Structure de fichiers /hana/shared créée pendant l’installation de SAP HANA](./media/hana-get-started/image025.jpg)

Cette capture d’écran illustre la structure de fichiers du répertoire /usr/sap.

![Structure de fichiers du répertoire/usr/sap](./media/hana-get-started/image026.jpg)

La dernière étape de l’installation distribuée ABAP concerne l’instance de serveur d’applications principal.

![Installation ABAP montrant la dernière étape : l’instance de serveur d’applications principal](./media/hana-get-started/image027b.jpg)

Une fois l’instance de serveur d’applications principal et l’interface graphique utilisateur SAP installés, utilisez la transaction « dbacockpit » pour vérifier que l’installation de SAP HANA s’est terminée correctement.

![Fenêtre DBA Cockpit confirmant la réussite de l’installation](./media/hana-get-started/image028b.jpg)

Pour terminer, vous pourriez vouloir installer d’abord SAP HANA Studio sur la machine virtuelle du serveur d’applications SAP et vous connecter ensuite à l’instance SAP HANA en cours d’exécution sur la machine virtuelle du serveur de base de données.

![Installation de SAP HANA Studio sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>Installation manuelle de SAP HANA avec l’outil de gestion du cycle de vie HANA (hdblcm)
Outre l’installation de SAP HANA dans le cadre d’une installation distribuée avec SWPM, vous pouvez installer d’abord HANA de façon autonome à l’aide de l’outil hdblcm. Une fois cette opération effectuée, vous pouvez installer par exemple SAP NetWeaver 7.5. Les captures d’écran suivantes illustrent le fonctionnement de ce processus.

Voici trois sources d’informations sur l’outil HANA hdblcm :

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Choisir l’outil SAP HANA HDBLCM adapté à votre tâche)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Outils de gestion du cycle de vie SAP HANA)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guide d’installation et de mise à jour du serveur SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Pour empêcher d’éventuels problèmes avec un paramètre d’ID de groupe par défaut pour l’utilisateur \<SID HANA\>adm (créé par l’outil hdblcm), définissez un nouveau groupe appelé « sapsys » avec l’ID de groupe 1001 avant d’installer SAP HANA via l’outil hdblcm.

![Nouveau groupe « sapsys » défini avec l’ID de groupe 1001](./media/hana-get-started/image030.jpg)

Lorsque vous démarrez hdblcm pour la première fois, un menu de démarrage simple s’affiche. Sélectionnez la première option, **Install new system** (Installer le nouveau système), comme illustré dans la capture d’écran suivante.

![Option « Install new system » (Installer le nouveau système) de la fenêtre de démarrage de l’outil hdblcm](./media/hana-get-started/image031.jpg)

La capture d’écran suivante montre toutes les options clés que vous avez sélectionnées précédemment.

> [!IMPORTANT]
> Les répertoires qui sont nommés pour les volumes de données et de journaux HANA, ainsi que le chemin d’installation (/hana/shared dans cet exemple) et /usr/sap, ne doivent pas être partie du système de fichiers racine. Ces répertoires appartiennent aux disques de données Azure qui ont été attachés à la machine virtuelle, comme décrit dans la section sur la configuration des machines virtuelles Azure. Cette approche permet d’éviter que le système de fichiers racine manque d’espace. Vous pouvez également voir que l’ID d’utilisateur de l’administrateur HANA est 1005 et que cet utilisateur fait partie du groupe sapsys (ID 1001) qui a été défini avant l’installation.

![Liste de tous les composants SAP HANA clés sélectionnés précédemment](./media/hana-get-started/image032.jpg)

Vous pouvez consulter les informations de l’utilisateur \<SID HANA\>adm (azdadm dans la capture d’écran suivante) dans /etc/passwd.

![Informations de l’utilisateur \<SID HANA\>adm répertoriées dans /etc/passwd](./media/hana-get-started/image033.jpg)

Après avoir installé SAP HANA à l’aide de l’outil hdblcm, vous pouvez voir la structure des fichiers dans SAP HANA Studio. Le schéma SAPABAP1, qui inclut toutes les tables SAP NetWeaver, n’est pas encore disponible.

![Structure des fichiers SAP HANA dans SAP HANA Studio](./media/hana-get-started/image034.jpg)

Après avoir installé SAP HANA, vous pouvez installer SAP NetWeaver par-dessus. Comme le montre cette capture d’écran, l’installation a été effectuée en tant qu’« installation distribuée » à l’aide de SWPM (en suivant la procédure décrite dans la section précédente). Lorsque vous installez l’instance de base de données à l’aide de SWPM, vous entrez les mêmes données que pour l’outil hdblcm (par exemple, le nom d’hôte, le SID HANA et le numéro d’instance). SWPM utilise ensuite l’installation HANA existante et ajoute plusieurs d’autres schémas.

![Installation distribuée effectuée à l’aide de SWPM](./media/hana-get-started/image035b.jpg)

La capture d’écran suivante illustre l’étape de l’installation avec SWPM au cours de laquelle les données relatives au schéma DBACOCKPIT sont saisies.

![Étape de l’installation avec SWPM au cours de laquelle les données du schéma DBACOCKPIT sont saisies](./media/hana-get-started/image036b.jpg)

Entrez les données relatives au schéma SAPABAP1.

![Saisie des données relatives au schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois que l’installation de l’instance de base de données avec SWPM est terminée, vous pouvez voir le schéma SAPABAP1 dans SAP HANA Studio.

![Schéma SAPABAP1 dans SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Enfin, une fois l’installation du serveur d’applications SAP et de l’interface utilisateur graphique SAP terminée, vous pouvez vérifier l’instance de base de données HANA à l’aide de la transaction « dbacockpit ».

![Vérification de l’instance de base de données HANA avec la transaction « dbacockpit »](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Téléchargement des logiciels SAP
Vous pouvez télécharger les logiciel à partir de SAP Service Marketplace, comme illustré dans les captures d’écran suivantes.

* Télécharger NetWeaver 7.5 pour Linux/HANA :

 ![Fenêtre Installation and Upgrade (Installation et mise à niveau) de SAP Service pour le téléchargement de NetWeaver 7.5](./media/hana-get-started/image001.jpg)
* Télécharger HANA SP12 Platform Edition :

 ![Fenêtre Installation and Upgrade (Installation et mise à niveau) de SAP Service pour le téléchargement de HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)


