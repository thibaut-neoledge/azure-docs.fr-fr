---
title: "Utilisation de l’API REST du service Azure Import/Export | Microsoft Docs"
description: "Découvrez où se trouvent les ressources pour l’utilisation de l’API REST du service Azure Import/Export, y compris des documents de référence et de procédure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e4f5ca289f4bd87574e448d37a1154b222f221f5
ms.lasthandoff: 03/30/2017


---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilisation de l’API REST du service Azure Import/Export

Le service Microsoft Azure Import/Export expose une API REST pour activer le contrôle par programme des travaux d’importation/exportation. Vous pouvez utiliser l’API REST pour exécuter toutes les opérations d’importation/exportation que vous pouvez effectuer avec le [portail Azure](https://portal.azure.com/). En outre, vous pouvez utiliser l’API REST pour effectuer certaines opérations granulaires, telles que l’interrogation du pourcentage d’achèvement d’un travail, qui ne sont pas disponibles actuellement dans le portail Classic.

Consultez [Transfert de données vers le stockage Blob à l’aide du service Microsoft Azure Import/Export](storage-import-export-service.md) pour une présentation du service Import/Export et un didacticiel qui explique comment utiliser le portail Classic pour créer et gérer les travaux d’importation et d’exportation.

## <a name="service-endpoints"></a>Points de terminaison de service

Le service Azure Import/Export est un fournisseur de ressources pour Azure Resource Manager et fournit un ensemble d’API REST au point de terminaison HTTPS suivant pour gérer les travaux d’importation/exportation :

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Contrôle de version

Les demandes au service d’importation/exportation doivent spécifier le paramètre `api-version` et définir sa valeur sur `2016-11-01`.

## <a name="importexport-service-operations"></a>Opérations du service Import/Export

[Création d’un travail d’importation](storage-import-export-creating-an-import-job.md)

[Création d’un travail d’exportation](storage-import-export-creating-an-export-job.md)

[Récupération des informations d’état d’un travail](storage-import-export-retrieving-state-info-for-a-job.md)

[Énumération des travaux](storage-import-export-enumerating-jobs.md)

[Annulation et suppression de travaux](storage-import-export-cancelling-and-deleting-jobs.md)

[Sauvegarde de manifestes de lecteur](storage-import-export-backing-up-drive-manifests.md)

[Diagnostic et récupération d’erreur pour les travaux d’importation / exportation](storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Étapes suivantes

* [API REST d’importation/exportation de stockage](/rest/api/storageimportexport)

