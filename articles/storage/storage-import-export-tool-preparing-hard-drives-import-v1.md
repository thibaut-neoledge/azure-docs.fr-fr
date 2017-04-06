---
title: "Préparation des disques durs pour un travail d’importation Azure Import/Export - v1 | Microsoft Docs"
description: "Découvrez comment préparer des disques durs à l’aide de l’outil WAImportExport v1 afin de créer une tâche d’importation pour le service Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 5ec2f1346e7c9723aeca45f1c278d0731b3a43b5
ms.lasthandoff: 03/30/2017


---

# <a name="preparing-hard-drives-for-an-import-job"></a>Préparation des disques durs pour un travail d’importation
Pour préparer un ou plusieurs disques durs pour un travail d’importation, procédez comme suit :

-   Identifier les données à importer dans le service BLOB

-   Identifier les répertoires virtuels cibles et les blobs dans le service BLOB

-   Déterminer le nombre de disques nécessaires

-   Copier les données sur chacun de vos disques durs

 Pour un exemple de workflow, consultez [Sample Workflow to Prepare Hard Drives for an Import Job (Exemple de workflow pour préparer des disques durs à un travail d’importation)](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identifier les données à importer
 La première étape pour créer une tâche d’importation consiste à déterminer les répertoires et les fichiers que vous allez importer. Il peut s’agir d’une liste de répertoires, d’une liste de fichiers uniques ou d’une combinaison des deux. Lorsqu’un répertoire est inclus, tous les fichiers situés dans ce répertoire et ses sous-répertoires font partie du travail d’importation.

> [!NOTE]
>  Étant donné que les sous-répertoires sont inclus de manière récursive lorsqu’un répertoire parent est inclus, spécifiez uniquement le répertoire parent. Ne spécifiez pas un de ses sous-répertoires.
>
>  Actuellement, l’outil Microsoft Azure Import/Export est soumis à la limitation suivante : si un répertoire contient plus de données que ne peut contenir un disque dur, alors le répertoire doit être divisé en répertoires plus petits. Par exemple, si un répertoire contient 2,5 To de données et que la capacité du disque dur est uniquement de 2 To, vous devez scinder le répertoire de 2,5 To en répertoires plus petits. Cette limitation sera supprimée dans une version ultérieure de l’outil.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identifier les emplacements de destination dans le service BLOB
 Pour chaque répertoire ou fichier à importer, vous devez identifier un répertoire virtuel ou un objet blob de destination dans le service BLOB Azure. Vous utiliserez ces cibles comme des entrées dans l’outil Azure Import/Export. Notez que les répertoires doivent être délimités par la barre oblique « / ».

 Le tableau suivant présente des exemples d’objets blob cibles :

|Fichier ou répertoire source|Répertoire virtuel ou objet blob cibles|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Déterminer le nombre de disques nécessaires
 Ensuite, vous devez déterminer :

-   Le nombre de disques durs nécessaires pour stocker les données.

-   Les répertoires et/ou les fichiers individuels à copier sur chacun de vos disques durs.

 Assurez-vous d’avoir le bon nombre de disques durs pour stocker les données que vous transférez.

## <a name="copy-data-to-your-hard-drive"></a>Copier les données sur votre disque dur
 Cette section décrit comment appeler l’outil Azure Import/Export pour copier vos données sur un ou plusieurs disques durs. Chaque fois que vous appelez l’outil Azure Import/Export, vous créez une *session de copie*. Vous créez au moins une session de copie pour chaque disque sur lequel vous copiez des données. Dans certains cas, plusieurs sessions de copie peuvent être nécessaires pour copier toutes vos données sur un seul disque. Voici quelques raisons pour lesquelles vous devrez peut-être créer plusieurs sessions de copie :

-   Vous devez créer une session de copie distincte pour chaque disque sur lequel vous souhaitez copier des données.

-   Une session de copie peut copier un répertoire unique ou un seul blob sur le disque. Si vous copiez plusieurs répertoires, plusieurs blobs ou une combinaison des deux, vous devrez créer plusieurs sessions de copie.

-   Vous pouvez spécifier les propriétés et les métadonnées qui seront définies sur les blobs importés dans le cadre d’un travail d’importation. Les propriétés ou les métadonnées que vous spécifiez pour une session de copie seront appliquées à tous les blobs spécifiés par cette session de copie. Si vous souhaitez indiquer des propriétés ou des métadonnées différentes pour certains blobs, vous devrez créer une session de copie distincte. Consultez [Setting Properties and Metadata during the Import Process (Définition des propriétés et métadonnées pendant le processus d’importation)](storage-import-export-tool-setting-properties-metadata-import-v1.md) pour plus d’informations.

> [!NOTE]
>  Si vous avez plusieurs ordinateurs qui répondent aux critères décrits dans [Setting Up the Azure Import-Export Tool (Configuration de l’outil Azure Import/Export)](storage-import-export-tool-setup-v1.md), vous pouvez copier des données sur plusieurs disques durs en parallèle en exécutant une instance de cet outil sur chaque ordinateur.

 Pour chaque disque dur que vous préparez avec l’outil Azure Import/Export, l’outil crée un fichier journal unique. Vous devez avoir les fichiers journaux de tous vos disques pour créer le travail d’importation. Le fichier journal permet également de reprendre la préparation du disque là où l’outil a été interrompu.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Syntaxe de l’outil Azure Import/Export pour un travail d’importation
 Pour préparer des disques en vue d’un travail d’importation, appelez l’outil Azure Import/Export avec la commande **PrepImport**. Les paramètres inclus varient selon s’il s’agit de la première session de copie ou non.

 La première session de copie d’un disque nécessite des paramètres supplémentaires pour spécifier la clé du compte de stockage, la lettre du disque cible, si le disque doit être formaté, si le disque doit être chiffré et le cas échéant, la clé BitLocker, et le répertoire de journaux. Voici la syntaxe pour une session de copie initiale afin de copier un répertoire ou un seul fichier :

 **Première session de copie pour copier un répertoire unique**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Première session de copie pour copier un fichier unique**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Dans les sessions de copie suivantes, il est inutile de spécifier les paramètres initiaux. Voici la syntaxe pour une session de copie suivante afin de copier un répertoire ou un seul fichier :

 **Sessions de copie suivantes pour copier un répertoire unique**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sessions de copie suivantes pour copier un fichier unique**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Paramètres de la première session de copie pour un disque dur
 Chaque fois que vous exécutez l’outil Azure Import/Export pour copier des fichiers sur le disque dur, l’outil crée une session de copie. Chaque session de copie copie un seul répertoire ou un seul fichier sur un disque dur. L’état de la session de copie est consigné dans le fichier journal. Si une session de copie est interrompue (par exemple, en raison d’une coupure de courant), elle peut être reprise en relançant l’outil et en spécifiant le fichier journal sur la ligne de commande.

> [!WARNING]
>  Si vous spécifiez le paramètre **/format** pour la première session de copie, le disque sera formaté et toutes les données du disque seront effacées. Nous vous recommandons d’utiliser des disques vides uniquement pour votre session de copie.

 La commande utilisée pour la première session de copie de chaque disque requiert des paramètres différents des commandes des sessions de copie suivantes. Le tableau suivant répertorie les paramètres supplémentaires qui sont disponibles pour la première session de copie :

|Paramètre de ligne de commande|Description|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` Clé du compte de stockage vers lequel les données sont importées. Vous devez inclure **/sk:**<StorageAccountKey\> ou **/csas:**<ContainerSas\> dans la commande.|
|**/csas:**<ContainerSas\>|`Optional`. SAP de conteneur à utiliser pour importer des données dans le compte de stockage. Vous devez inclure **/sk:**<StorageAccountKey\> ou **/csas:**<ContainerSas\> dans la commande.<br /><br /> La valeur de ce paramètre doit commencer par le nom du conteneur, suivi d’un point d’interrogation (?) et du jeton SAP. Par exemple :<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Les autorisations, qu’elles soient spécifiées sur l’URL ou dans une stratégie d’accès stocké, doivent inclure Read, Write et Delete pour les travaux d’importation et Read, Write et List pour les travaux d’exportation.<br /><br /> Lorsque ce paramètre est renseigné, tous les blobs à importer ou à exporter doivent être dans le conteneur spécifié dans la signature d’accès partagé.|
|**/t:**<TargetDriveLetter\>|`Required.` Lettre de lecteur du disque dur cible pour la session de copie actuelle, sans les deux-points de fin.|
|**/format**|`Optional.` Spécifiez ce paramètre quand le disque doit être formaté, sinon, ignorez-le. Avant de formater le disque, l’outil demande confirmation à partir de la console. Pour supprimer la confirmation, spécifiez le paramètre /silentmode.|
|**/silentmode**|`Optional.` Spécifiez ce paramètre pour supprimer la confirmation du formatage du disque cible.|
|**/encrypt**|`Optional.` Spécifiez ce paramètre quand le disque n’a pas encore été chiffré avec BitLocker et doit être chiffré par l’outil. Si le disque a déjà été chiffré avec BitLocker, ignorez ce paramètre et spécifiez le paramètre `/bk`, en fournissant la clé BitLocker existante.<br /><br /> Si vous spécifiez le paramètre `/format`, vous devez également spécifier le paramètre `/encrypt`.|
|**/bk:**<BitLockerKey\>|`Optional.`Si `/encrypt` est spécifié, omettez ce paramètre. Si `/encrypt` est omis, vous devez déjà avoir chiffré le disque avec BitLocker. Utilisez ce paramètre pour spécifier la clé BitLocker. Le chiffrement BitLocker est requis pour tous les disques durs pour les travaux d’importation.|
|**/logdir:**<LogDirectory\>|`Optional.` Le répertoire de journaux désigne le répertoire à utiliser pour stocker les journaux détaillés et les fichiers manifestes temporaires. Si ce paramètre n’est pas spécifié, le répertoire courant est utilisé comme répertoire de journaux.|

### <a name="parameters-required-for-all-copy-sessions"></a>Paramètres requis pour toutes les sessions de copie
 Le fichier journal contient l’état de toutes les sessions de copie pour un disque dur. Il contient également les informations nécessaires à la création du travail d’importation. Vous devez toujours spécifier un fichier journal lorsque vous exécutez l’outil Azure Import/Export, ainsi qu’un ID de session de copie :

|||
|-|-|
|Paramètre de ligne de commande|Description|
|**/j:**<JournalFile\>|`Required.` Chemin d’accès au fichier journal. Chaque disque doit avoir un seul fichier journal. Notez que le fichier journal ne doit pas résider sur le disque cible. L’extension de fichier journal est `.jrn`.|
|**/id:**<SessionId\>|`Required.` L’ID de session identifie une session de copie. Il est utilisé pour assurer la précision de la récupération d’une session de copie interrompue. Les fichiers copiés dans une session de copie sont stockés dans un répertoire dont le nom fait référence à l’ID de session sur le disque cible.|

### <a name="parameters-for-copying-a-single-directory"></a>Paramètres de copie d’un seul répertoire
 Lorsque vous copiez un seul répertoire, les paramètres obligatoires et facultatifs suivants s’appliquent :

|Paramètre de ligne de commande|Description|
|----------------------------|-----------------|
|**/srcdir:**<SourceDirectory\>|`Required.` Répertoire source qui contient les fichiers à copier sur le disque cible. Le chemin d’accès du répertoire doit être un chemin d’accès absolu (et non relatif).|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` Chemin d’accès au répertoire virtuel de destination dans votre compte de stockage Azure. Le répertoire virtuel peut déjà exister ou non.<br /><br /> Vous pouvez spécifier un conteneur ou un préfixe de blob comme `music/70s/`. Le répertoire de destination doit commencer par le nom du conteneur, suivi d’une barre oblique « / » et peut inclure un répertoire virtuel de blob se terminant par « / ».<br /><br /> Lorsque le conteneur de destination est le conteneur racine, vous devez spécifier explicitement le conteneur racine, y compris la barre oblique tel que `$root/`. Comme les blobs du conteneur racine ne peuvent pas inclure « / » dans leur nom, les sous-répertoires du répertoire source ne sont pas copiés si le répertoire de destination est le conteneur racine.<br /><br /> Veillez à utiliser des noms de conteneur valides quand vous spécifiez des répertoires virtuels de destination ou des objets blob. N’oubliez pas que les noms de conteneur doivent être en minuscules. Pour connaître les règles d’affectation de noms aux conteneurs, consultez [Naming and Referencing Containers, Blobs, and Metadata (Affectation de noms et références aux conteneurs, blobs et métadonnées)](/rest/api/storageservices/fileservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` Spécifie le comportement lorsqu’un blob avec l’adresse spécifiée existe déjà. Les valeurs valides pour ce paramètre sont : `rename`, `no-overwrite` et `overwrite`. Notez que ces valeurs sont sensibles à la casse. Si aucune valeur n’est spécifiée, la valeur par défaut est `rename`.<br /><br /> La valeur spécifiée pour ce paramètre affecte tous les fichiers dans le répertoire spécifié par le paramètre `/srcdir`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Spécifie le type de blob pour les blobs de destination. Les valeurs autorisées sont : `BlockBlob` et `PageBlob`. Notez que ces valeurs sont sensibles à la casse. Si aucune valeur n’est spécifiée, la valeur par défaut est `BlockBlob`.<br /><br /> Dans la plupart des cas, `BlockBlob` est recommandé. Si vous spécifiez `PageBlob`, la longueur de chaque fichier dans le répertoire doit être un multiple de 512, la taille d’une page pour les blobs de pages.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Chemin d’accès au fichier des propriétés des blobs de destination. Pour plus d’informations, consultez [Format de fichier de propriétés et de métadonnées du service d’importation/exportation](storage-import-export-file-format-metadata-and-properties.md).|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Chemin d’accès au fichier des métadonnées des blobs de destination. Pour plus d’informations, consultez [Format de fichier de propriétés et de métadonnées du service d’importation/exportation](storage-import-export-file-format-metadata-and-properties.md).|

### <a name="parameters-for-copying-a-single-file"></a>Paramètres de copie d’un seul fichier
 Lorsque vous copiez un seul fichier, les paramètres obligatoires et facultatifs suivants s’appliquent :

|Paramètre de ligne de commande|Description|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` Chemin d’accès complet au fichier à copier. Le chemin d’accès du répertoire doit être un chemin d’accès absolu (et non relatif).|
|**/dstblob:**<DestinationBlobPath\>|`Required.` Chemin d’accès au blob de destination dans votre compte de stockage Azure. Le blob peut déjà exister ou non.<br /><br /> Spécifiez le nom du blob commençant par le nom du conteneur. Le nom du blob ne peut pas commencer par « / » ni le nom du compte de stockage. Pour connaître les règles d’affectation de noms aux blobs, consultez [Naming and Referencing Containers, Blobs, and Metadata (Affectation de noms et références aux conteneurs, blobs et métadonnées)](/rest/api/storageservices/fileservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Lorsque le conteneur de destination est le conteneur racine, vous devez spécifier explicitement `$root` comme conteneur, tel que `$root/sample.txt`. Notez que les blobs sous le conteneur racine ne peuvent pas inclure « / » dans leur nom.|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` Spécifie le comportement lorsqu’un blob avec l’adresse spécifiée existe déjà. Les valeurs valides pour ce paramètre sont : `rename`, `no-overwrite` et `overwrite`. Notez que ces valeurs sont sensibles à la casse. Si aucune valeur n’est spécifiée, la valeur par défaut est `rename`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Spécifie le type de blob pour les blobs de destination. Les valeurs autorisées sont : `BlockBlob` et `PageBlob`. Notez que ces valeurs sont sensibles à la casse. Si aucune valeur n’est spécifiée, la valeur par défaut est `BlockBlob`.<br /><br /> Dans la plupart des cas, `BlockBlob` est recommandé. Si vous spécifiez `PageBlob`, la longueur de chaque fichier dans le répertoire doit être un multiple de 512, la taille d’une page pour les blobs de pages.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Chemin d’accès au fichier des propriétés des blobs de destination. Pour plus d’informations, consultez [Format de fichier de propriétés et de métadonnées du service d’importation/exportation](storage-import-export-file-format-metadata-and-properties.md).|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Chemin d’accès au fichier des métadonnées des blobs de destination. Pour plus d’informations, consultez [Format de fichier de propriétés et de métadonnées du service d’importation/exportation](storage-import-export-file-format-metadata-and-properties.md).|

### <a name="resuming-an-interrupted-copy-session"></a>Reprise d’une session de copie interrompue
 Si une session de copie est interrompue pour une raison quelconque, vous pouvez la reprendre en exécutant l’outil avec le fichier journal spécifié :

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Seule la dernière session de copie, si elle s’est terminée anormalement, peut être reprise.

> [!IMPORTANT]
>  Lorsque vous reprenez une session de copie, ne modifiez pas les fichiers et répertoires source.

### <a name="aborting-an-interrupted-copy-session"></a>Annulation d’une session de copie interrompue
 Si une session de copie est interrompue et qu’il n’est pas possible de la reprendre (par exemple, si un répertoire source est inaccessible), vous devez l’annuler pour pouvoir lancer d’autres sessions de copie :

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Seule la dernière session de copie, si elle s’est terminée anormalement, peut être annulée. Notez que vous ne pouvez pas annuler la première session de copie d’un disque. Mais, vous devez redémarrer la session de copie avec un nouveau fichier journal.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration de l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Définition des propriétés et métadonnées pendant le processus d’importation](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Exemple de workflow pour préparer des disques durs à un travail d’importation](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Référence rapide pour les commandes fréquemment utilisées](storage-import-export-tool-quick-reference-v1.md) 
* [Consultation de l’état du travail avec les fichiers journaux de copie](storage-import-export-tool-reviewing-job-status-v1.md)
* [Réparation d’un travail d’importation](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Réparation d’un travail d’exportation](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Résolution des problèmes associés à l’outil Azure Import-Export](storage-import-export-tool-troubleshooting-v1.md)

