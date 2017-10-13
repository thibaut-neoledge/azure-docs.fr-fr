---
title: "Configuration de l’outil Azure Import-Export | Microsoft Docs"
description: "Découvrez comment configurer l’outil de préparation et de réparation de disques pour le service Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 6b5febd051d0b956c90cb14c260dda1881adac3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-the-azure-importexport-tool"></a>Configuration de l’outil Azure Import/Export

L’outil Microsoft Azure Import/Export est l’outil de préparation et de réparation de disques, que vous pouvez utiliser avec le service Microsoft Azure Import/Export. Vous pouvez utiliser l’outil pour les fonctions suivantes :

* Avant de créer un travail d’importation, l’outil vous permet de copier des données sur des disques durs que vous allez envoyer à un centre de données Azure.
* Lorsqu’un travail d’importation est terminé, vous pouvez utiliser cet outil pour réparer les objets blob endommagés, manquants ou en conflit avec d’autres objets blob.
* Lorsque vous recevez les disques d’un travail d’exportation, vous pouvez utiliser cet outil pour réparer tous les fichiers endommagés ou manquants sur les disques.

## <a name="prerequisites"></a>Composants requis

Si vous **préparez des disques** pour un travail d’importation, vous devez remplir les conditions préalables suivantes :

* Vous devez avoir un abonnement Azure actif.
* Votre abonnement doit inclure un compte de stockage avec suffisamment d’espace disponible pour stocker les fichiers que vous allez importer.
* Vous devez avoir au moins une des clés d’accès au compte de stockage.
* Vous devez avoir un ordinateur (« ordinateur de copie ») avec Windows 7, Windows Server 2008 R2 ou un système d’exploitation Windows plus récent installé.
* .NET Framework 4 doit être installé sur l’ordinateur de copie.
* BitLocker doit être activé sur l’ordinateur de copie.
* Vous avez besoin d’un ou plusieurs disques durs SATA II ou III, ou SSD, 2,5 ou 3,5 pouces, vides et connectés à l’ordinateur de copie.
* Les fichiers que vous projetez d’importer doivent être accessibles à l’ordinateur de copie, qu’ils soient sur un partage réseau ou un disque dur local.

Si vous essayez de **réparer une importation** qui a partiellement échoué, vous devez disposer des éléments suivants :

* fichiers journaux de copie,
* clé du compte de stockage.

Si vous essayez de **réparer une exportation** qui a partiellement échoué, vous devez disposer des éléments suivants :

* fichiers journaux de copie,
* fichiers de manifeste (facultatifs),
* clé du compte de stockage.

## <a name="installing-the-azure-importexport-tool"></a>Installation de l’outil Azure Import/Export

Tout d’abord, [téléchargez l’outil Azure Import/Export](https://www.microsoft.com/download/details.aspx?id=55280) et extrayez-le dans un répertoire sur votre ordinateur, par exemple `c:\WAImportExport`.

L’outil Azure Import/Export comprend les fichiers suivants :

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Ensuite, ouvrez une fenêtre d’invite de commandes en **mode Administrateur** et accédez au répertoire contenant les fichiers extraits.

Pour générer l’aide relative à la commande, exécutez l’outil (`WAImportExport.exe`) sans paramètres :

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Étapes suivantes

* [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Consultation de l’état du travail avec les fichiers journaux de copie](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Réparation d’un travail d’importation](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Réparation d’un travail d’exportation](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Résolution des problèmes associés à l’outil Azure Import-Export](storage-import-export-tool-troubleshooting-v1.md)
