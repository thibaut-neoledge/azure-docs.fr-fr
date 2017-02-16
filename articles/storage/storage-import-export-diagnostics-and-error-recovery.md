---
title: "Diagnostic et récupération d’erreur pour les travaux Azure Import/Export | Microsoft Docs"
description: "Découvrez comment activer la journalisation documentée pour les travaux du service Microsoft Azure Import/Export"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 060e8cbbd812ed2495d1c589f29760d2453af51b


---

# <a name="diagnostics-and-error-recovery-for-import-export-jobs"></a>Diagnostic et récupération d’erreur pour les travaux d’importation/exportation
Pour chaque disque traité, le service Azure Import/Export crée un journal d’erreurs dans le compte de stockage associé. Vous pouvez également activer la journalisation documentée en définissant la propriété `LogLevel` sur `Verbose` lors de l’appel des opérations [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update).

 Par défaut, les journaux sont écrits dans un conteneur nommé `waimportexport`. Vous pouvez spécifier un autre nom en définissant la propriété `DiagnosticsPath` lors de l’appel des opérations `Put Job` ou `Update Job Properties`. Les journaux sont stockés sous la forme d’objets blob de blocs avec la convention d’affectation de noms suivante : `waies/jobname_driveid_timestamp_logtype.xml`.

 Vous pouvez récupérer l’URI des journaux pour un travail en appelant l’opération [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). L’URI pour le journal documenté est retourné dans la propriété `VerboseLogUri` pour chaque disque, alors que l’URI pour le journal d’erreurs est retourné dans la propriété `ErrorLogUri`.

Vous pouvez utiliser les données de journalisation pour identifier les problèmes suivants :

**Erreurs de disque**

-   Erreurs d’accès ou de lecture du fichier de manifeste

-   Clés BitLocker incorrectes

-   Erreurs de lecture/écriture sur le disque

**Erreurs d’objet blob**

-   Objet blob ou nom incorrect ou conflictuel

-   Fichiers manquants

-   Objet blob introuvable

-   Fichiers tronqués (les fichiers sur le disque sont plus petits que ceux spécifiés dans le manifeste)

-   Contenu de fichier endommagé (pour les travaux d’importation, détecté par une incompatibilité de la somme de contrôle MD5)

-   Fichiers de propriétés et de métadonnées blob endommagés (détectés par une incompatibilité de la somme de contrôle MD5)

-   Schéma incorrect des fichiers de propriétés et/ou de métadonnées blob

Il peut arriver que certaines parties d’un travail d’importation ou d’exportation n’aboutisse pas, alors que l’ensemble du travail est terminé. Dans ce cas, vous pouvez charger ou télécharger les parties manquantes des données sur le réseau, ou vous pouvez créer un nouveau travail pour transférer les données. Consultez la [référence sur l’outil Azure Import/Export](storage-import-export-tool-how-to-v1.md) pour découvrir comment corriger les données sur le réseau.

## <a name="see-also"></a>Voir aussi
[Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


