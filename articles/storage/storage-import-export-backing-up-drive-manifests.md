---
title: Sauvegarde de manifestes de lecteur Azure Import/Export | Microsoft Docs
description: "Découvrez comment sauvegarder automatiquement vos manifestes de lecteur pour le service Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: c215112cc73f1378ba3ec2c7099b102aaf56db63
ms.openlocfilehash: 43c31b965f9f1471bce9fe4b9ac32a392b6ecbb1
ms.lasthandoff: 02/16/2017


---

# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Sauvegarde de manifestes de lecteur pour les travaux Azure Import/Export
Les manifestes de lecteur peuvent être automatiquement sauvegardés vers des objets blob en définissant la propriété `BackupDriveManifest` sur `true` dans les opérations [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). Par défaut, les manifestes de lecteur ne sont pas sauvegardés. Les sauvegardes de manifestes de lecteur sont stockées en tant qu’objets blob de blocs dans un conteneur au sein du compte de stockage associé au travail. Par défaut, le nom du conteneur est `waimportexport`, mais vous pouvez spécifier un autre nom dans la propriété `DiagnosticsPath` lors de l’appel des opérations `Put Job` ou `Update Job Properties`. Les objets blob de manifeste sauvegardés sont nommés selon le format suivant : `waies/jobname_driveid_timestamp_manifest.xml`.

 Vous pouvez récupérer l’URI des manifestes de lecteur sauvegardés pour un travail en appelant l’opération [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). L’URI de l’objet blob est retourné dans la propriété `ManifestUri` pour chaque lecteur.

## <a name="see-also"></a>Voir aussi
 [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)

