---
title: "Sauvegarde et restauration du système d’exploitation pour les références (SKU) de type II SAP HANA (grandes instances) sur Azure | Microsoft Docs"
description: "Effectuer la sauvegarde et la restauration du système d’exploitation pour les références (SKU) de type II SAP HANA (grandes instances) sur Azure"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41349cd7fe3bf39b5b42c44ba47acf980d15ebe7
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Sauvegarde et la restauration du système d’exploitation pour les références (SKU) de type II

Ce document décrit les étapes permettant de sauvegarder et de restaurer le système d’exploitation pour les **références (SKU) de type II** des grandes instances HANA. 

>[!NOTE]
>Les scripts de sauvegarde du système d’exploitation utilisent le logiciel ReaR, qui est préinstallé sur le serveur.  

Une fois le provisionnement effectué par l’équipe de gestion des services Microsoft, le serveur est, par défaut, configuré avec une planification de deux sauvegardes pour sauvegarder le système d’exploitation complet. Vous pouvez vérifier la planification des tâches de sauvegarde avec la commande suivante :
```
#crontab –l
```
Vous pouvez modifier la planification de la sauvegarde à tout moment avec la commande suivante :
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Comment effectuer une sauvegarde manuelle ?

La sauvegarde du système d’exploitation est d’ores et déjà planifiée avec une **tâche Cron**. Toutefois, vous pouvez aussi effectuer la sauvegarde du système d’exploitation manuellement. Pour effectuer une sauvegarde manuelle, exécutez la commande suivante :

```
#rear -v mkbackup
```
La capture d’écran suivante montre l’exemple d’une sauvegarde manuelle :

![procédure](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Comment restaurer une sauvegarde ?

Vous pouvez restaurer une sauvegarde complète ou un fichier individuel à partir de la sauvegarde. Pour effectuer la restauration, utilisez la commande suivante :

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Après la restauration, le fichier est récupéré dans le répertoire de travail actuel.

La commande suivante illustre la restauration d’un fichier */etc/fstab* à partir du fichier de sauvegarde *backup.tar.gz*.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Vous devez copier le fichier dans l’emplacement souhaité après sa restauration à partir de la sauvegarde.

La capture d’écran suivante présente la restauration d’une sauvegarde complète :

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Comment installer l’outil ReaR et modifier la configuration ? 

Les packages ReaR (Relax-and-Recover) sont **préinstallés** dans les **références (SKU) de type II** des grandes instances HANA, et aucune action de votre part n’est nécessaire. Vous pouvez commencer directement à utiliser ReaR pour la sauvegarde du système d’exploitation.
Toutefois, dans le cas où vous devez installer vous-même les packages, vous pouvez suivre les étapes répertoriées pour installer et configurer l’outil ReaR.

Pour installer les packages de sauvegarde **ReaR**, utilisez les commandes suivantes :

Pour le système d’exploitation **SLES**, utilisez la commande suivante :
```
#zypper install <rear rpm package>
```
Pour le système d’exploitation **RHEL**, utilisez la commande suivante : 
```
#yum install rear -y
```
Pour configurer l’outil ReaR, vous devez mettre à jour les paramètres **OUTPUT_URL** et **BACKUP_URL** dans le *fichier /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

La capture d’écran suivante présente la restauration d’une sauvegarde complète : ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
