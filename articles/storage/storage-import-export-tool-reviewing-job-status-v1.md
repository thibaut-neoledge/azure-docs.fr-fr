---
title: "Vérification de l’état des travaux Azure Import/Export | Microsoft Docs"
description: "Découvrez comment utiliser les fichiers journaux créés lors de l’exécution du travail d’importation ou d’exportation pour connaître l’état du travail Import-Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: f0d445e51ff91f6edc219522ec3c12a86dc4ee98
ms.lasthandoff: 02/16/2017


---

# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Vérification de l’état des travaux Azure Import/Export avec les copies des fichiers journaux
Lorsque le service Microsoft Azure Import/Export traite des lecteurs associés à un travail d’importation ou d’exportation, il écrit les fichiers journaux de copie dans le compte de stockage dans ou à partir duquel vous importez ou exportez des objets blob. Le fichier journal contient l’état détaillé de chaque fichier importé ou exporté. L’URL de chaque fichier journal de copie est renvoyée lorsque vous interrogez l’état d’un travail effectué ; consultez la page [Get Job](/rest/api/storageservices`Get-Job3) pour plus d’informations.  
  
 Voici des exemples d’URL de fichiers journaux de copie pour un travail d’importation avec deux lecteurs :  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Consultez la page [Format des fichiers journaux du Service Import-Export](storage-import-export-file-format-log.md) pour connaître le format des journaux de copie et la liste complète des codes d’état.  
  
## <a name="see-also"></a>Voir aussi  
 [Configuration de l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 [Préparation des disques durs pour un travail d’importation](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [Réparation d’un travail d’importation](storage-import-export-tool-repairing-an-import-job-v1.md)   
 [Réparation d’un travail d’exportation](storage-import-export-tool-repairing-an-export-job-v1.md)   
 [Résolution des problèmes associés à l’outil Azure Import-Export](storage-import-export-tool-troubleshooting-v1.md)

