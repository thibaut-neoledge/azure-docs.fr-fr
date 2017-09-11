---
title: "Aide-mémoire sur les commandes de travail d’importation pour l’outil Azure Import/Export | Microsoft Docs"
description: "Référence de l’outil Azure Import/Export pour les commandes de travail d’importation fréquemment utilisées."
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
ms.date: 01/15/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: d51ae35ead0e7d8289de663e5b7b48d28271e810
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Aide-mémoire sur les commandes fréquemment utilisées pour les travaux d’importation

Cet article fournit un aide-mémoire pour certaines commandes fréquemment utilisées. Pour une utilisation détaillée, consultez [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Première session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Deuxième session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Annuler la dernière session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Reprise de la dernière session interrompue

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Ajouter des disques à la dernière session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Étapes suivantes

* [Exemple de workflow pour préparer des disques durs à un travail d’importation](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)

