---
title: Sauvegarde de manifestes de lecteur Azure Import/Export | MicrosoftDocs
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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 3d3abfb93ba09aa9f09258743ef6d4c1fc932ae5


---

# <a name="backing-up-drive-manifests"></a>Sauvegarde de manifestes de lecteur
Les manifestes de lecteur peuvent être automatiquement sauvegardés vers des objets blob en définissant la propriété `BackupDriveManifest` sur `true` dans les opérations [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). Par défaut, les manifestes de lecteur ne sont pas sauvegardés. Les sauvegardes de manifestes de lecteur sont stockées en tant qu’objets blob de blocs dans un conteneur au sein du compte de stockage associé au travail. Par défaut, le nom du conteneur est `waimportexport`, mais vous pouvez spécifier un autre nom dans la propriété `DiagnosticsPath` lors de l’appel des opérations `Put Job` ou `Update Job Properties`. Les objets blob de manifeste sauvegardés sont nommés selon le format suivant : `waies/jobname_driveid_timestamp_manifest.xml`.

 Vous pouvez récupérer l’URI des manifestes de lecteur sauvegardés pour un travail en appelant l’opération [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). L’URI de l’objet blob est retourné dans la propriété `ManifestUri` pour chaque lecteur.

## <a name="see-also"></a>Voir aussi
 [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


