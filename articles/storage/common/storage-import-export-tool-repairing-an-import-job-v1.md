---
title: "Réparation d’un travail d’importation Azure Import/Export - v1 | Microsoft Docs"
description: "Apprenez à réparer un travail d’importation qui a été créé et exécuté à l’aide du service Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: c837713fd9e7d03287ae5a3644fd6bb47714c9d4
ms.contentlocale: fr-fr
ms.lasthandoff: 08/23/2017

---

# <a name="repairing-an-import-job"></a>Réparation d’un travail d’importation
Le service Microsoft Azure Import/Export peut ne pas copier certains de vos fichiers ou des parties d’un fichier dans le service BLOB Windows Azure. Voici quelques exemples de raisons de ces échecs :  
  
-   Fichiers endommagés  
  
-   Disques endommagés  
  
-   La clé du compte de stockage a été modifiée pendant le transfert du fichier.  
  
Vous pouvez exécuter l’outil Microsoft Azure Import/Export avec les fichiers journaux de copie du travail d’importation. L’outil charge alors les fichiers (ou les parties d’un fichier) manquants dans votre compte de stockage Microsoft Azure pour terminer la tâche d’importation.  
  
## <a name="repairimport-parameters"></a>Paramètres de RepairImport

Les paramètres suivants peuvent être spécifiés pour **RepairImport** : 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Obligatoire.** Chemin d’accès au fichier de réparation, qui suit la progression de la réparation et vous permet de reprendre une réparation interrompue. Chaque disque ne doit avoir qu’un fichier de réparation. Quand vous lancez la réparation d’un lecteur donné, transmettez le chemin d’un fichier de réparation, qui n’existe pas encore. Pour reprendre une réparation interrompue, vous devez transmettre le nom d’un fichier de réparation existant. Le fichier de réparation correspondant au disque cible doit toujours être spécifié.|  
|**/logdir:**<LogDirectory\>|**Facultatif.** Répertoire contenant les journaux. Les fichiers journaux détaillés sont écrits dans ce répertoire. Si aucun répertoire de journaux n’est spécifié, le répertoire actif est utilisé comme répertoire de journaux.|  
|**/d:**<TargetDirectories\>|**Obligatoire.** Un ou plusieurs répertoires séparés par des points-virgules contenant les fichiers d’origine qui ont été importés. Le disque d’importation peut également être utilisé, mais il n’est pas obligatoire si d’autres emplacements de fichiers d’origine sont disponibles.|  
|**/bk:**<BitLockerKey\>|**Facultatif.** Vous devez spécifier la clé BitLocker si vous souhaitez que l’outil déverrouille un disque chiffré dans lequel les fichiers d’origine sont disponibles.|  
|**/sn:**<StorageAccountName\>|**Obligatoire.** Nom du compte de stockage du travail d’importation.|  
|**/sk:**<StorageAccountKey\>|**Obligatoire** si et seulement si un SAP de conteneur n’est pas spécifié. Clé du compte de stockage du travail d’importation.|  
|**/csas:**<ContainerSas\>|**Obligatoire** si et seulement si la clé du compte de stockage n’est pas spécifiée. SAP de conteneur pour accéder aux objets blob associés au travail d’importation.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Obligatoire.** Chemin d’accès au fichier journal de copie du disque (journal détaillé ou d’erreurs). Le fichier est généré par le service Windows Azure Import/Export et peut être téléchargé à partir du stockage blob associé au travail. Le fichier journal de copie contient des informations sur les objets blob ou les fichiers en échec qui doivent être réparés.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Facultatif.** Chemin d’accès à un fichier texte qui peut être utilisé pour résoudre les ambiguïtés si vous importez dans le même travail plusieurs fichiers portant le même nom. La première fois que l’outil est exécuté, il peut remplir ce fichier avec tous les noms ambigus. Les exécutions suivantes de l’outil utilisent ce fichier pour résoudre les ambiguïtés.|  
  
## <a name="using-the-repairimport-command"></a>Utilisation de la commande RepairImport  
Pour réparer des données d’importation en diffusant en continu les données sur le réseau, vous devez spécifier les répertoires qui contiennent les fichiers d’origine que vous importez à l’aide du paramètre `/d`. Vous devez également spécifier le fichier journal de copie que vous avez téléchargé à partir de votre compte de stockage. Une ligne de commande classique pour réparer un travail d’importation avec des échecs partiels est semblable à :  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
Dans l’exemple suivant d’un fichier journal de copie, une partie de 64 Ko d’un fichier a été endommagée sur le disque qui a été envoyé pour le travail d’importation. Dans la mesure où il s’agit de la seule erreur indiquée, les autres objets blob du travail ont bien été importés.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Quand ce journal de copie est transmis à l’outil Azure Import/Export, celui-ci essaie de terminer l’importation de ce fichier en copiant le contenu manquant via le réseau. Dans l’exemple ci-dessus, l’outil recherche le fichier d’origine `\animals\koala.jpg` dans les deux répertoires (`C:\Users\bob\Pictures` et `X:\BobBackup\photos`). Si le fichier `C:\Users\bob\Pictures\animals\koala.jpg` existe, l’outil Azure Import/Export copie la plage de données manquante dans l’objet blob correspondant `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Résolution des conflits lors de l’utilisation de RepairImport  
Dans certains cas, l’outil peut ne pas être en mesure de trouver ou d’ouvrir le fichier nécessaire pour l’une des raisons suivantes : le fichier est introuvable, le fichier n’est pas accessible, le nom de fichier est ambigu ou le contenu du fichier n’est plus approprié.  
  
Une erreur ambiguë peut se produire si l’outil essaie de localiser `\animals\koala.jpg` et s’il existe un fichier portant ce nom dans `C:\Users\bob\pictures` et `X:\BobBackup\photos`. Autrement dit, `C:\Users\bob\pictures\animals\koala.jpg` et `X:\BobBackup\photos\animals\koala.jpg` existent sur les disques de travail d’importation.  
  
L’option `/PathMapFile` vous permet de résoudre ces erreurs. Vous pouvez spécifier le nom du fichier qui contient la liste des fichiers que l’outil n’a pas pu identifier correctement. L’exemple de ligne de commande suivant remplit `9WM35C2V_pathmap.txt` :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
L’outil écrit ensuite les chemins d’accès de fichier problématique dans `9WM35C2V_pathmap.txt`, à raison d’un par ligne. Par exemple, le fichier peut contenir les entrées suivantes après avoir exécuté la commande :  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Pour chaque fichier de la liste, vous devez essayer de localiser et d’ouvrir le fichier pour vous assurer qu’il est disponible pour l’outil. Si vous souhaitez indiquer explicitement à l’outil où rechercher un fichier, vous pouvez modifier le fichier de mappage de chemin d’accès et ajouter le chemin d’accès à chaque fichier sur la même ligne, séparé par un caractère de tabulation :  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Après avoir mis les fichiers nécessaires à disposition de l’outil ou après avoir mis à jour le fichier de mappage de chemin d’accès, vous pouvez réexécuter l’outil pour terminer le processus d’importation.  
  
## <a name="next-steps"></a>Étapes suivantes
 
* [Configuration de l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Consultation de l’état du travail avec les fichiers journaux de copie](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Réparation d’un travail d’exportation](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Résolution des problèmes associés à l’outil Azure Import-Export](storage-import-export-tool-troubleshooting-v1.md)

