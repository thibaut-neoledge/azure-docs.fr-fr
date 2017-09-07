---
title: "Démarrage rapide : installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 05fb31007e1e4c2243f93169129ec5b2c93099e2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Démarrage rapide : installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure
## <a name="introduction"></a>Introduction
Ce guide vous aide à configurer un système SAP HANA à instance unique sur des machines virtuelles Azure pendant l’installation manuelle de SAP NetWeaver 7.5 et SAP HANA 1.0 SP12. Ce guide se concentre sur le déploiement de SAP HANA sur Azure. Il ne remplace pas la documentation SAP. 

>[!Note]
>Ce guide décrit des déploiements de SAP HANA sur des machines virtuelles Azure. Pour plus d’informations sur le déploiement de SAP HANA sur des instances de HANA de grande taille, consultez [Utilisation de SAP sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Prérequis
Ce guide suppose que vous maîtrisiez certaines bases d’IaaS (infrastructure as a service), notamment :
 * Comment déployer des machines virtuelles ou des réseaux virtuels en utilisant le portail Azure ou PowerShell.
 * L’interface de ligne de commande multiplateforme (CLI) Azure, y compris la possibilité d’utiliser des modèles JSON (JavaScript Object Notation).

Ce guide suppose également que vous êtes familiarisé avec :
* Les systèmes SAP HANA et SAP NetWeaver et comment les installer localement.
* L’installation et l’utilisation d’instances d’application SAP HANA et SAP sur Azure.
* Les concepts et les procédures suivants :
   * Planification du déploiement de SAP sur Azure, y compris la planification du réseau virtuel Azure et l’utilisation du stockage Azure. Consultez [SAP NetWeaver sur machines virtuelles Azure – Guide de planification et d’implémentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Principes de déploiement et modalités de déploiement sur des machines virtuelles dans Azure. Consultez [Déploiement de machines virtuelles Azure pour SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Haute disponibilité pour SAP NetWeaver ASC (ABAP SAP Central Services), SCS (SAP Central Services) et ERS (Evaluated Receipt Settlement) sur Azure. Consultez [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Détails sur la façon d’améliorer l’efficacité en tirant parti d’une installation multi-SID d’ASCS/SCS sur Azure. Consultez [Créer une configuration SAP NetWeaver multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principes de l’exécution de SAP NetWeaver basée sur des machines virtuelles pilotées par Linux dans Azure. Consultez [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ce guide fournit quelques-uns des paramètres spécifiques à Linux dans les machines virtuelles Azure et explique comment joindre des disques de stockage Azure à des machines virtuelles Linux.

À l’heure actuelle, les machines virtuelles Azure sont certifiées par SAP pour les configurations de montée en puissance SAP HANA uniquement. Les configurations incluant une augmentation de la taille des instances avec une charge de travail SAP HANA ne sont pas encore prises en charge. Pour la haute disponibilité de SAP HANA dans les configurations de montée en puissance, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Si vous voulez déployer très rapidement une instance de SAP HANA ou un système S/4HANA ou BW/4HANA, vous devez envisager l’utilisation de la [bibliothèque d’appliances cloud SAP](http://cal.sap.com). Vous trouverez dans [ce guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) de la documentation sur le déploiement, par exemple, d’un système S/4HANA par le biais de la bibliothèque SAP CAL sur Azure. Il suffit que vous ayez un abonnement Azure et qu’un utilisateur SAP puisse être enregistré dans la bibliothèque d’appliances cloud SAP.

## <a name="additional-resources"></a>Ressources supplémentaires
### <a name="sap-hana-backup"></a>Sauvegarde SAP HANA
Pour plus d’informations sur la sauvegarde des bases de données SAP HANA sur des machines virtuelles Azure, consultez :
* [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Sauvegarde SAP HANA sur Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Bibliothèque d’appliances cloud SAP
Pour plus d’informations sur l’utilisation de la bibliothèque d’appliances cloud SAP pour déployer S/4HANA ou BW/4HANA, consultez [Déploiement de SAP S/4HANA ou BW/4HANA sur Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Systèmes d’exploitation pris en charge par SAP HANA
Pour plus d’informations sur les systèmes d’exploitation pris en charge par SAP HANA, consultez la [note de support SAP #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (SAP HANA : Systèmes d’exploitation pris en charge). Les machines virtuelles Azure ne prennent en charge qu’un sous-ensemble de ces systèmes d’exploitation. Les systèmes d’exploitation suivants sont pris en charge pour déployer SAP HANA sur Azure : 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Pour obtenir de la documentation SAP supplémentaire sur SAP HANA et les différents systèmes d’exploitation Linux, consultez :

* [Note de support SAP #171356 relative aux logiciels SAP sur Linux : informations générales](https://launchpad.support.sap.com/#/notes/1984787)
* [Note de support SAP #1944799 relative aux instructions SAP HANA pour l’installation du système d’exploitation SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Note de support SAP #2205917 relative aux paramètres de système d’exploitation SAP HANA DB recommandés pour SLES 12 ainsi que les application SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Note de support SAP #1984787 relative à SUSE Linux Enterprise Server 12 : notes d’installation](https://launchpad.support.sap.com/#/notes/1984787)
* [Note de support SAP #1391070 relative aux solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070)
* [Note de support SAP #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879) (Instructions SAP HANA pour les systèmes d’exploitation Red Hat Enterprise Linux (RHEL))
* [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E) (SAP HANA DB : Paramètres de système d’exploitation recommandés pour RHEL 7)

### <a name="sap-monitoring-in-azure"></a>Surveillance SAP dans Azure
Pour plus d’informations sur la surveillance SAP dans Azure, consultez :

* [Note de SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) Cette note aborde la « surveillance améliorée » SAP avec des machines virtuelles Linux sur Azure. 
* [Note de SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Cette note contient des informations relatives à SAPOSCOL sur Linux. 
* [Note de SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Cette note aborde des métriques de surveillance clés pour SAP sur Microsoft Azure.

### <a name="azure-vm-types"></a>Types de machines virtuelles Azure
Les types de machines virtuelles Azure et les scénarios de charge de travail pris en charge par SAP utilisés par SAP HANA sont documentés dans [SAP certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) (plateforme IaaS certifiée SAP). 

Les types de machines virtuelles Azure certifiées par SAP pour SAP NetWeaver ou la couche Application S/4HANA sont documentées dans [Note de support SAP 1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Applications SAP sur Azure : produits et types de machines virtuelles pris en charge).

>[!Note]
>L’intégration SAP-Linux-Azure est prise en charge uniquement sur Azure Resource Manager ; le modèle de déploiement classique n’est pas compatible. 

## <a name="manual-installation-of-sap-hana"></a>Installation manuelle de SAP HANA
Ce guide explique comment installer manuellement SAP HANA sur des machines virtuelles Azure de deux manières :

* En utilisant SAP Software Provisioning Manager (SWPM) dans le cadre d’une installation NetWeaver distribuée à l’étape d’installation de l’instance de base de données
* En utilisant l’outil de gestion du cycle de vie des bases de données SAP HANA, HDBLCM, puis en installant NetWeaver

Vous pouvez également utiliser SWPM pour installer tous les composants (SAP HANA, le serveur d’applications SAP et l’instance ASCS) sur une seule machine virtuelle, comme décrit dans cette [annonce de blog SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Cette option n’est pas décrite dans ce guide de démarrage rapide, mais les points que vous devez prendre en considération sont les mêmes.

Avant de commencer une installation, nous vous recommandons de lire la section « Préparation des machines virtuelles Azure pour une installation manuelle de SAP HANA » plus loin dans ce guide. Cela vous aidera à prévenir plusieurs erreurs de base qui peuvent survenir lorsque vous utilisez seulement une configuration de machine virtuelle Azure par défaut.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Étapes clés de l’installation de SAP HANA quand vous utilisez SAP SWPM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique lorsque vous utilisez SAP SWMP pour réaliser une installation distribuée de SAP NetWeaver 7.5. Les différentes étapes sont expliquées plus en détail dans les captures d’écran plus loin dans ce guide.

1. Créer un réseau virtuel Azure qui inclut deux machines virtuelles de test.
2. Déployer les deux machines virtuelles Azure avec les systèmes d’exploitation (dans notre exemple, il s’agit de SUSE Linux Enterprise Server (SLES) et SLES for SAP Applications 12 SP1), selon le modèle Azure Resource Manager.
3. Joindre deux disques de stockage Azure standard ou premium (par exemple, des disques de 75 Go ou 500 Go) à la machine virtuelle du serveur d’applications.
4. Joindre des disques de stockage premium à la machine virtuelle du serveur HANA DB. Pour plus d’informations, consultez la section « Configuration des disques » plus loin dans ce guide.
5. Selon les exigences de taille ou de débit, joindre plusieurs disques, puis créer des volumes agrégés par bandes en utilisant la gestion de volumes logiques ou un outil d’administration de périphériques multiples (MDADM) au niveau du système d’exploitation à l’intérieur de la machine virtuelle.
6. Créer des systèmes de fichiers XFS sur les disques ou volumes logiques joints.
7. Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour tous les logiciels SAP. Utiliser l’autre pour le répertoire /sapmnt et les sauvegardes, par exemple. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour empêcher la saturation du système de fichiers racine, peu volumineux sur les machines virtuelles Linux Azure.
8. Entrer les adresses IP locales des machines virtuelles de test dans le fichier /etc/hosts.
9. Entrer le paramètre **nofail** dans le fichier /etc/fstab.
10. Définir les paramètres de noyau Linux en fonction de la version Release du système d’exploitation Linux que vous utilisez. Pour plus d’informations, consultez les notes SAP appropriées qui abordent HANA et la section « Paramètres de noyau » dans ce guide.
11. Ajouter un espace d’échange.
12. Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
13. Télécharger les logiciels SAP à partir de SAP Service Marketplace.
14. Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
15. Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
16. Avec l’outil SWPM, installer l’instance de base de données, y compris HANA, sur la machine virtuelle du serveur de base de données.
17. Installer le serveur d’applications principal (PAS) sur la machine virtuelle du serveur d’applications.
18. Démarrer la console de gestion SAP. Se connecter avec l’interface graphique utilisateur SAP ou HANA Studio, par exemple.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Étapes clés de l’installation de SAP HANA quand vous utilisez HDBLCM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique quand vous utilisez SAP HDBLCM pour réaliser une installation distribuée de SAP NetWeaver 7.5. Les différentes étapes sont expliquées plus en détail dans les captures d’écran tout au long de ce guide.

1. Créer un réseau virtuel Azure qui inclut deux machines virtuelles de test.
2. Déployer deux machines virtuelles Azure avec les systèmes d’exploitation (dans notre exemple, il s’agit de SLES et SLES for SAP Applications 12 SP1) selon le modèle Azure Resource Manager.
3. Joindre deux disques de stockage Azure standard ou premium (par exemple, des disques de 75 Go ou 500 Go) à la machine virtuelle du serveur d’applications.
4. Joindre des disques de stockage premium à la machine virtuelle du serveur HANA DB. Pour plus d’informations, consultez la section « Configuration des disques » plus loin dans ce guide.
5. Selon les exigences de taille ou de débit, joindre plusieurs disques et créer des volumes agrégés par bandes en utilisant la gestion de volumes logiques ou un outil d’administration de périphériques multiples (MDADM) au niveau du système d’exploitation à l’intérieur de la machine virtuelle.
6. Créer des systèmes de fichiers XFS sur les disques ou volumes logiques joints.
7. Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour tous les logiciels SAP et l’autre pour le répertoire /sapmnt et les sauvegardes, par exemple. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour empêcher la saturation du système de fichiers racine, peu volumineux sur les machines virtuelles Linux Azure.
8. Entrer les adresses IP locales des machines virtuelles de test dans le fichier /etc/hosts.
9. Entrer le paramètre **nofail** dans le fichier /etc/fstab.
10. Définir les paramètres de noyau en fonction de la version Release du système d’exploitation Linux que vous utilisez. Pour plus d’informations, consultez les notes SAP appropriées qui abordent HANA et la section « Paramètres de noyau » dans ce guide.
11. Ajouter un espace d’échange.
12. Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
13. Télécharger les logiciels SAP à partir de SAP Service Marketplace.
14. Créer un groupe, sapsys, avec l’ID de groupe 1001 sur la machine virtuelle du serveur de base de données HANA.
15. Installer SAP HANA sur la machine virtuelle du serveur de base de données à l’aide du gestionnaire de cycle de vie des bases de données HANA (HDBLCM).
16. Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
17. Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
18. Avec l’outil SWPM, installer l’instance de base de données, y compris HANA, sur la machine virtuelle du serveur de base de données HANA.
19. Installer le serveur d’applications principal (PAS) sur la machine virtuelle du serveur d’applications.
20. Démarrer la console de gestion SAP. Se connecter par le biais de l’interface graphique utilisateur SAP ou HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Préparation des machines virtuelles Azure pour une installation manuelle de SAP HANA
Cette section couvre les sujets suivants :

* Mises à jour de système d’exploitation
* Configuration des disques
* Paramètres de noyau
* Systèmes de fichiers
* Fichier /etc/hosts
* Fichier /etc/fstab

### <a name="os-updates"></a>Mises à jour de système d’exploitation
Vérifiez les mises à jour et les correctifs du système d’exploitation Linux avant d’installer des logiciels supplémentaires. En installant un correctif, vous pourrez peut-être éviter un appel au support technique.

Assurez-vous d’utiliser :
* SUSE Linux Enterprise Server pour applications SAP ;
* Red Hat Enterprise Linux pour applications SAP ou Red Hat Enterprise Linux pour SAP HANA. 

Si vous ne l’avez pas déjà fait, enregistrez le déploiement du système d’exploitation dans votre abonnement Linux auprès du fournisseur de Linux. Notez que SUSE a des images de système d’exploitation pour les applications SAP qui incluent déjà des services et qui sont enregistrées automatiquement.

Voici un exemple de recherche de correctifs disponibles pour SUSE Linux à l’aide de la commande **zypper** :

 `sudo zypper list-patches`

Suivant le genre de problème, les correctifs sont classés par catégorie et par gravité. Parmi les valeurs couramment utilisées pour la catégorie figurent **security**, **recommended**, **optional**, **feature**, **document** ou **yast**.
Parmi les valeurs couramment utilisées pour la gravité figurent **critical**, **important**, **moderate**, **low** ou **unspecified**.

La commande **zypper** recherche uniquement les mises à jour nécessaires aux packages installés. Par exemple, vous pouvez utiliser cette commande :

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Vous pouvez ajouter le paramètre `--dry-run` pour tester la mise à jour sans réellement mettre à jour le système.


### <a name="disk-setup"></a>Configuration des disques
La taille du système de fichiers racine d’une machine virtuelle Linux sur Azure est limitée. Par conséquent, il est nécessaire d’attacher un espace disque supplémentaire à une machine virtuelle Azure pour l’exécution de SAP. Pour des machines virtuelles Azure de serveur d’applications SAP, l’utilisation de disques de stockage Azure standard peut être suffisante. Toutefois, pour des machines virtuelles Azure SGBD SAP HANA, l’utilisation de disques de stockage Azure Premium dans les implémentations de production et hors production est obligatoire.

En fonction des [exigences de stockage TDI SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), la configuration Azure Premium suivante est suggérée : 

| Référence de la machine virtuelle | RAM |  /hana/data et /hana/log <br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 Go | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Dans la configuration de disque suggérée, le volume de données et le volume de journal HANA sont placés sur le même ensemble de disques de stockage Azure premium qui sont agrégés avec LVM ou MDADM. Il n’est pas nécessaire de définir un niveau de redondance RAID, car le stockage Azure Premium conserve trois images des disques à des fins de redondance. Pour vérifier que vous configurez suffisamment de stockage, consultez [SAP HANA TDI storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Exigences de stockage TDI SAP HANA) et [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guide d’installation et de mise à jour du serveur SAP HANA). Prenez également en compte les différents volumes de débit de disque dur virtuel (VHD) des divers disques de stockage Azure premium, comme expliqué dans [Stockage Premium hautes performances et disques managés pour machines virtuelles](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Vous pouvez ajouter des disques de stockage premium aux machines virtuelles HANA SGBD, pour stocker les sauvegardes du journal des transactions ou de bases de données.

Pour plus d’informations sur les deux principaux outils utilisés pour configurer l’agrégation par bandes, consultez les articles suivants :

* [Configuration d’un RAID logiciel sur Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurer LVM sur une machine virtuelle Linux dans Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pour plus d’informations sur la façon de joindre des disques à des machines virtuelles Azure exécutant Linux comme système d’exploitation invité, consultez [Ajouter un disque à une machine virtuelle Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Le stockage Azure Premium vous permet de définir les modes de mise en cache des disques. Pour l’ensemble de disques agrégés contenant /hana/data et /hana/log, la mise en cache des disques doit être désactivée. Pour les autres volumes (disques), le mode de mise en cache doit être défini sur **ReadOnly**.

Pour plus d’informations, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../../../storage/common/storage-premium-storage.md) .

Pour rechercher des exemples de modèles JSON pour la création de machines virtuelles, accédez à [Modèles de démarrage rapide Microsoft Azure](https://github.com/Azure/azure-quickstart-templates).
Le modèle vm-simple-sles est un modèle de base. Il inclut une section de stockage, avec un disque de données de 100 Go supplémentaires. Ce modèle peut être utilisé comme base. Vous pouvez adapter le modèle à votre propre configuration.

>[!Note]
>Il est essentiel de joindre le disque de stockage Azure à l’aide d’un UUID, comme expliqué dans [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dans l’environnement de test, deux disques de stockage standard Azure ont été attachés à la machine virtuelle du serveur applications SAP, comme illustré dans la capture d’écran suivante. Un des disques était utilisé pour stocker tous les logiciels SAP (notamment NetWeaver 7.5, GUI SAP et SAP HANA) à installer. Le second disque permettait de garantir un espace disponible suffisant pour les besoins supplémentaires (par exemple, pour les données de sauvegarde et de test) et pour le répertoire /sapmnt (c’est-à-dire les profils SAP) à partager entre toutes les machines virtuelles appartenant au même paysage SAP.

![Fenêtre des disques du serveur d’applications SAP HANA montrant deux disques de données et leur taille](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Paramètres de noyau
SAP HANA nécessite des paramètres de noyau Linux spécifiques, qui ne font pas partie des images de galerie Azure standard et doivent être définis manuellement. Selon que vous utilisez SUSE ou Red Hat, les paramètres peuvent différer. Les notes SAP mentionnées précédemment donnent des informations sur ces paramètres. Dans les captures d’écran présentées, SUSE Linux 12 SP1 a été utilisé. 

SLES for SAP Applications 12 GA et SLES for SAP Applications 12 SP1 offrent un nouvel outil, **tuned-adm**, qui remplace l’ancien outil **sapconf**. Un profil SAP HANA spécial est disponible pour **tuned-adm**. Pour paramétrer le système pour SAP HANA, entrez les informations suivantes en tant qu’utilisateur racine :

   `tuned-adm profile sap-hana`

Pour plus d’informations sur **tuned-adm**, consultez la [documentation de SUSE sur tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Dans la capture d’écran suivante, vous pouvez voir comment **tuned-adm** a modifié les valeurs `transparent_hugepage` et `numa_balancing` en fonction des paramètres SAP HANA requis.

![L’outil tuned-adm modifie les valeurs en fonction des paramètres SAP HANA requis](./media/hana-get-started/image005.jpg)

Pour conserver les paramètres de noyau SAP HANA, utilisez **grub2** sur SLES 12. Pour plus d’informations sur **grub2**, accédez à la section [Configuration File Structure](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) (Structure du fichier de configuration) de la documentation SUSE.

La capture d’écran suivante montre comment les paramètres de noyau ont été modifiés dans le fichier config, puis compilés à l’aide de **grub2-mkconfig** :

![Paramètres de noyau modifiés dans le fichier config et compilés à l’aide de grub2-mkconfig](./media/hana-get-started/image006.jpg)

Vous pouvez également modifier les paramètres à l’aide de YaST et des réglages de **Chargeur de démarrage** > **Paramètres du kernel** :

![Onglet Paramètres du kernel dans le chargeur de démarrage YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systèmes de fichiers
La capture d’écran suivante montre les deux systèmes de fichiers qui ont été créés sur la machine virtuelle du serveur d’applications SAP, sur les deux disques de stockage standard Azure. Les deux systèmes de fichiers présentent le type XFS et sont montés sur /sapdata et /sapsoftware.

Vous n’êtes pas obligé de structurer vos systèmes de fichiers de cette façon. D’autres options sont disponibles pour structurer l’espace disque. Le plus important est d’empêcher le système de fichiers racine de manquer d’espace libre.

![Deux systèmes de fichiers créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image008.jpg)

En ce qui concerne la machine virtuelle SAP HANA DB, pendant une installation de base de données, quand vous utilisez SAPinst (SWPM) et l’option d’installation **typical** (par défaut), tous les éléments sont installés sous /hana et /usr/sap. L’emplacement par défaut de la sauvegarde des fichiers journaux SAP HANA se trouve sous /usr/sap. Là encore, comme il est important d’empêcher le système de fichiers racine de manquer d’espace de stockage, assurez-vous qu’il y a suffisamment d’espace libre sous /hana et /usr/sap avant d’installer SAP HANA à l’aide de SWPM.

Pour obtenir une description de la disposition de système de fichiers standard de SAP HANA, consultez le document [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guide d’installation et de mise à jour du serveur SAP HANA).

![Systèmes de fichiers supplémentaires créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image009.jpg)

Durant l’installation de SAP NetWeaver sur une image de galerie Azure SLES/SLES for SAP Applications 12 standard, un message s’affiche pour indiquer l’absence d’espace d’échange, comme l’illustre la capture d’écran suivante. Pour faire disparaître ce message, vous pouvez ajouter manuellement un fichier d’échange à l’aide des options **dd**, **mkswap** et **swapon**. Pour en savoir plus, recherchez « Adding a Swap File Manually » (Ajout manuel d’un fichier d’échange) dans la section [Using the YaST Partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) (Utilisation du partitionneur YaST) de la documentation SUSE.

Vous pouvez également configurer l’espace d’échange à l’aide de l’agent de machine virtuelle Linux. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![Message contextuel indiquant que l’espace d’échange est insuffisant](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Fichier /etc/hosts
Avant de commencer l’installation de SAP, pensez à inclure les noms d’hôtes et les adresses IP des machines virtuelles SAP dans le fichier /etc/hosts. Déployez toutes les machines virtuelles SAP dans un même réseau virtuel Azure, puis utilisez les adresses IP internes, comme illustré ci-dessous :

![Noms d’hôte et adresses IP des machines virtuelles SAP répertoriés dans le fichier/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Fichier /etc/fstab

Il est utile d’ajouter le paramètre **nofail** au fichier fstab. En effet, en cas de problème avec les disques, la machine virtuelle ne reste pas bloquée dans le processus de démarrage. Cependant, gardez à l’esprit que l’espace disque supplémentaire n’est peut-être pas disponible et les processus peuvent saturer le système de fichiers racine. Si /hana est manquant, SAP HANA ne démarrera pas.

![Ajouter le paramètre nofail au fichier fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Bureau graphique GNOME sur SLES 12/SLES for SAP Applications 12
Cette section couvre les sujets suivants :

* Installation du bureau GNOME et de xrdp sur SLES 12/SLES for SAP Applications 12
* Exécution de la console de gestion SAP basée sur Java avec Firefox sur SLES 12/SLES for SAP Applications 12

Vous pouvez également utiliser des alternatives telles que Xterminal ou VNC (non décrites dans ce guide).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installation du bureau GNOME et de xrdp sur SLES 12/SLES for SAP Applications 12
Si vous êtes habitué à Windows, vous pouvez facilement utiliser un bureau graphique directement dans les machines virtuelles Linux de SAP pour exécuter Firefox, SAPinst, l’interface graphique utilisateur SAP, la console de gestion SAP ou HANA Studio et vous connecter avec le protocole RDP (Remote Desktop Protocol) à partir d’un ordinateur Windows. En fonction de vos stratégies d’entreprise sur l’ajout d’interfaces utilisateur graphiques aux systèmes Linux de production et de non-production, vous souhaiterez peut-être installer GNOME sur votre serveur. Pour installer le bureau GNOME sur une machine virtuelle Azure SLES 12/SLES for SAP Applications 12 :

1. Installez le bureau GNOME en entrant la commande suivante (dans une fenêtre PuTTY, par exemple) :

   `zypper in -t pattern gnome-basic`

2. Installez xrdp pour autoriser l’établissement d’une connexion à la machine virtuelle via RDP :

   `zypper in xrdp`

3. Modifiez /etc/sysconfig/windowmanager et définissez GNOME comme gestionnaire de fenêtrage par défaut :

   `DEFAULT_WM="gnome"`

4. Exécutez la commande **chkconfig** pour vous assurer que xrdp démarre automatiquement après un redémarrage :

   `chkconfig -level 3 xrdp on`

5. En cas de problème avec la connexion RDP, essayez de redémarrer (à partir d’une fenêtre PuTTY, par exemple) :

   `/etc/xrdp/xrdp.sh restart`

6. Si un redémarrage xrdp mentionné à l’étape précédente ne fonctionne pas, déterminez si un fichier .pid est présent :

   `check /var/run` 

   Recherchez `xrdp.pid`. Si vous le trouvez, supprimez-le et essayez de redémarrer à nouveau.

### <a name="starting-sap-mc"></a>Démarrage de la console de gestion SAP
Une fois le bureau GNOME installé, une erreur peut s’afficher au démarrage de la console de gestion SAP graphique basée sur Java à partir de Firefox sur une machine virtuelle Azure SLES 12/SLES for SAP Applications 12 en raison de l’absence du plug-in de navigateur Java.

L’URL de démarrage de la console de gestion SAP est `<server>:5<instance_number>13`.

Pour plus d’informations, consultez [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm) (Démarrage de la console de gestion web SAP).

La capture d’écran suivante illustre le message d’erreur qui s’affiche quand le plug-in de navigateur Java est manquant :

![Message d’erreur indiquant que le plug-in de navigateur Java est manquant](./media/hana-get-started/image013.jpg)

Pour résoudre le problème, une solution consiste à installer le plug-in manquant à l’aide de YaST, comme illustré dans la capture d’écran suivante :

![Utilisation de YaST pour installer le plug-in manquant](./media/hana-get-started/image014.jpg)

Quand vous entrez de nouveau l’URL de la console de gestion SAP, un message s’affiche vous demandant d’activer le plug-in :

![Boîte de dialogue demandant l’activation du plug-in](./media/hana-get-started/image015.jpg)

Un message d’erreur concernant un fichier manquant, à savoir javafx.properties, peut également s’afficher. Cela est lié à l’exigence d’Oracle Java 1.8 pour la version 7.4 de l’interface utilisateur graphique de SAP. (Consultez la [note de SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Ni la version IBM Java ni le package openjdk fourni avec SLES/SLES for SAP Applications 12 ne comprend le fichier javafx.properties nécessaire. La solution consiste à télécharger et installer Java SE 8 auprès d’Oracle.

Pour plus d’informations sur un problème similaire avec openjdk sur openSUSE, consultez le thread de discussion [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306) (SAPGui 7.4 Java pour openSUSE 42.1 Leap).

## <a name="manual-installation-of-sap-hana-swpm"></a>Installation manuelle de SAP HANA : SWPM
La série de captures d’écran de cette section illustre les principales étapes d’installation de SAP NetWeaver 7.5 et SAP HANA SP12 avec SWPM (SAPinst). Lors de l’installation de NW 7.5, SWPM permet également d’installer la base de données HANA en tant qu’instance unique.

Dans un environnement de test, nous avons installé un seul serveur d’application ABAP (Advanced Business Application Programming). Comme illustré dans la capture d’écran suivante, nous avons utilisé l’option **Distributed System** (Système distribué) pour installer l’instance ASCS et l’instance de serveur d’applications principal sur une machine virtuelle Azure, et SAP HANA en tant que système de base de données sur une autre machine virtuelle Azure.

![Installation de l’instance ASCS et de l’instance de serveur d’applications principal à l’aide de l’option Distributed System (Système distribué)](./media/hana-get-started/image012.jpg)

Une fois que l’instance ASCS est installée sur la machine virtuelle du serveur d’applications et signalée en vert dans la console de gestion SAP (illustrée dans la capture d’écran ci-dessous), le répertoire /sapmnt (qui inclut notamment le répertoire des profils SAP) doit être partagé avec la machine virtuelle du serveur de base de données SAP HANA. L’étape d’installation de la base de données requiert l’accès à ces informations. Pour fournir cet accès, le meilleur moyen consiste à utiliser NFS, qui peut être configuré à l’aide de YaST.

![Console de gestion SAP montrant l’instance ASCS installée sur la machine virtuelle du serveur d’applications et signalée en vert](./media/hana-get-started/image016.jpg)

Sur la machine virtuelle du serveur d’applications, le répertoire /sapmnt doit être partagé via NFS à l’aide des options **rw** et **no_root_squash**. Les options par défaut sont **ro** et **root_squash**, qui peuvent provoquer des problèmes durant l’installation de l’instance de base de données.

![Partage du répertoire /sapmnt via NFS à l’aide des options rw et no_root_squash](./media/hana-get-started/image017b.jpg)

Comme la capture d’écran suivante le montre, le partage du répertoire /sapmnt de la machine virtuelle du serveur d’applications doit être configuré sur la machine virtuelle du serveur de base de donnée SAP HANA par le biais du **client NFS** (et YaST).

![Partage /sapmnt configuré à l’aide du client NFS](./media/hana-get-started/image018b.jpg)

Pour effectuer une installation distribuée de NetWeaver 7.5 (**Instance de base de données**), comme illustré dans la capture d’écran suivante, connectez-vous à la machine virtuelle du serveur de base de données SAP HANA et démarrez SWPM.

![Installation d’une instance de base de données en se connectant à la machine virtuelle du serveur de base de données SAP HANA et en démarrant SWPM](./media/hana-get-started/image019.jpg)

Après avoir sélectionné l’option d’installation **typical** (par défaut) et le chemin d’accès au support d’installation, entrez un identificateur de sécurité (SID) de base de données, le nom d’hôte, le numéro d’instance et le mot de passe d’administrateur système de la base de données.

![Page de connexion de l’administrateur système de la base de données SAP HANA](./media/hana-get-started/image035b.jpg)

Entrez le mot de passe pour le schéma DBACOCKPIT :

![Zone de saisie du mot de passe pour le schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Entrer une question pour le mot de passe du schéma SAPABAP1 :

![Entrer une question pour le mot de passe du schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois chaque tâche terminée, une coche verte est affichée en regard de chaque phase du processus d’installation de la base de données. Le message « Execution of ... Database Instance has completed. » (L’exécution de... Instance de base de données est terminée.) s’affiche.

![Fenêtre de la tâche terminée avec un message de confirmation](./media/hana-get-started/image023.jpg)

Après l’installation, la console de gestion SAP doit également signaler l’instance de base de données en vert et afficher la liste complète des processus SAP HANA (hdbindexserver, hdbcompileserver, etc.).

![Fenêtre de la console de gestion SAP montrant la liste des processus SAP HANA](./media/hana-get-started/image024.jpg)

La capture d’écran suivante montre les éléments de la structure de fichiers sous le répertoire /hana/shared que SWPM a créés durant l’installation de SAP HANA. Étant donné qu’il n’y a aucun moyen de spécifier un autre chemin d’accès, il est important de monter un espace disque supplémentaire sous le répertoire /hana avant d’installer SAP HANA à l’aide de SWPM. Cette opération empêche le système de fichiers racine de manquer d’espace libre.

![Structure de fichiers du répertoire /hana/shared créée pendant l’installation de SAP HANA](./media/hana-get-started/image025.jpg)

Cette capture d’écran illustre la structure de fichiers du répertoire /usr/sap :

![Structure de fichiers du répertoire/usr/sap](./media/hana-get-started/image026.jpg)

La dernière étape de l’installation distribuée ABAP consiste à installer l’instance de serveur d’applications principal :

![Installation ABAP montrant la dernière étape : l’instance de serveur d’applications principal](./media/hana-get-started/image027b.jpg)

Une fois installées l’instance de serveur d’applications principal et l’interface graphique utilisateur SAP, utilisez la transaction **DBA Cockpit** pour vérifier que l’installation de SAP HANA s’est terminée correctement :

![Fenêtre DBA Cockpit confirmant la réussite de l’installation](./media/hana-get-started/image028b.jpg)

Pour terminer, vous pourriez vouloir installer d’abord HANA Studio sur la machine virtuelle du serveur d’applications SAP et vous connecter ensuite à l’instance SAP HANA en cours d’exécution sur la machine virtuelle du serveur de base de données :

![Installation de SAP HANA Studio sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Installation manuelle de SAP HANA : HDBLCM
Outre l’installation de SAP HANA dans le cadre d’une installation distribuée avec SWPM, vous pouvez installer d’abord HANA de façon autonome, à l’aide de l’outil HDBLCM. Vous pouvez ensuite installer SAP NetWeaver 7.5, par exemple. Les captures d’écran de cette section illustrent le fonctionnement de ce processus.

Pour plus d’informations sur l’outil HANA HDBLCM, consultez :

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Choisir l’outil SAP HANA HDBLCM adapté à votre tâche)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Outils de gestion du cycle de vie SAP HANA)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guide d’installation et de mise à jour du serveur SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Pour empêcher d’éventuels problèmes avec un paramètre d’ID de groupe par défaut pour `\<HANA SID\>adm user` (créé par l’outil HDBLCM), définissez un nouveau groupe appelé `sapsys` avec l’ID de groupe `1001` avant d’installer SAP HANA via l’outil HDBLCM :

![Nouveau groupe « sapsys » défini avec l’ID de groupe 1001](./media/hana-get-started/image030.jpg)

Quand vous démarrez HDBLCM pour la première fois, un menu de démarrage simple s’affiche. Sélectionnez la première option, **Install new system** (Installer le nouveau système), comme illustré dans la capture d’écran suivante :

![Option « Install new system » (Installer le nouveau système) de la fenêtre de démarrage de l’outil HDBLCM](./media/hana-get-started/image031.jpg)

La capture d’écran suivante montre toutes les options clés que vous avez sélectionnées précédemment.

> [!IMPORTANT]
> Les répertoires qui sont nommés pour les volumes de données et de journaux HANA, ainsi que le chemin d’installation (/hana/shared dans cet exemple) et /usr/sap, ne doivent pas être partie du système de fichiers racine. Ces répertoires appartiennent aux disques de données Azure qui ont été joints à la machine virtuelle (voir la section « Configuration des disques »). Cette approche empêche le système de fichiers racine de manquer d’espace. Dans la capture d’écran suivante, vous pouvez voir que l’administrateur système HANA a l’identifiant utilisateur `1005` et qu’il fait partie du groupe `sapsys` (ID `1001`) qui a été défini avant l’installation.

![Liste de tous les composants SAP HANA clés sélectionnés précédemment](./media/hana-get-started/image032.jpg)

Vous pouvez vérifier les informations de `\<HANA SID\>adm user` (`azdadm` dans la capture d’écran ci-dessous) dans le répertoire /etc/passwd :

![Informations de l’utilisateur \<SID HANA\>adm répertoriées dans le répertoire /etc/passwd](./media/hana-get-started/image033.jpg)

Après avoir installé SAP HANA à l’aide de l’outil HDBLCM, vous pouvez voir la structure des fichiers dans SAP HANA Studio, comme l’illustre la capture d’écran ci-dessous. Le schéma SAPABAP1, qui inclut toutes les tables SAP NetWeaver, n’est pas encore disponible.

![Structure des fichiers SAP HANA dans SAP HANA Studio](./media/hana-get-started/image034.jpg)

Après avoir installé SAP HANA, vous pouvez installer SAP NetWeaver par-dessus. Comme le montre la capture d’écran ci-dessous, l’installation a été effectuée en tant qu’installation distribuée à l’aide de SWPM (en suivant la procédure décrite dans la section précédente). Quand vous installez l’instance de base de données à l’aide de SWPM, vous entrez les mêmes données que pour l’outil HDBLCM (par exemple, le nom d’hôte, le SID HANA et le numéro d’instance). SWPM utilise ensuite l’installation HANA existante et ajoute plusieurs d’autres schémas.

![Installation distribuée effectuée à l’aide de SWPM](./media/hana-get-started/image035b.jpg)

La capture d’écran suivante illustre l’étape de l’installation avec SWPM au cours de laquelle les données relatives au schéma DBACOCKPIT sont entrées :

![Étape de l’installation avec SWPM au cours de laquelle les données du schéma DBACOCKPIT sont saisies](./media/hana-get-started/image036b.jpg)

Entrez les données relatives au schéma SAPABAP1 :

![Saisie des données relatives au schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois que l’installation de l’instance de base de données avec SWPM est terminée, vous pouvez voir le schéma SAPABAP1 dans SAP HANA Studio :

![Schéma SAPABAP1 dans SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Enfin, une fois terminée l’installation du serveur d’applications SAP et de l’interface utilisateur graphique SAP, vous pouvez vérifier l’instance de base de données HANA à l’aide de la transaction **DBA Cockpit**.

![Vérification de l’instance de base de données HANA avec la transaction DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Téléchargement des logiciels SAP
Vous pouvez télécharger les logiciel à partir de SAP Service Marketplace, comme illustré dans les captures d’écran suivantes.

Télécharger NetWeaver 7.5 pour Linux/HANA :

 ![Fenêtre Installation and Upgrade (Installation et mise à niveau) de SAP Service pour le téléchargement de NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Télécharger HANA SP12 Platform Edition :

 ![Fenêtre Installation and Upgrade (Installation et mise à niveau) de SAP Service pour le téléchargement de HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)


