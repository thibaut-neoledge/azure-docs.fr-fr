---

title: "Format de fichier journal du service d’importation/exportation | Microsoft Docs"
description: "En savoir plus sur le format des fichiers journaux créés lors de l’exécution des étapes d’un travail du service d’importation/exportation"
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
ms.date: 05/25/2015
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 78abb839badf99c6251673ee9914955df8c950bc
ms.openlocfilehash: fab36b750ec4ba518fad8392f611cd46d8155985


---
# <a name="import-export-service-log-file-format"></a>Format de fichier journal du service d’importation/exportation
Lorsque le service Microsoft Azure Import/Export exécute une action sur un lecteur dans le cadre d’un travail d’importation ou d’exportation, les journaux sont écrits pour bloquer des objets blob dans le compte de stockage associé à ce travail.  
  
Il existe deux journaux pouvant être écrits par le service d’importation/exportation :  
  
-   Le journal d’erreurs est toujours généré en cas d’erreur.  
  
-   Le journal détaillé n’est pas activé par défaut, mais il peut être activé en définissant la propriété `EnableVerboseLog` sur une opération [Put Job](/rest/api/storageservices/importexport/Put-Job) ou [Update Job Properties](/rest/api/storageservices/importexport/Update-Job-Properties).  
  
## <a name="log-file-location"></a>Emplacement du fichier journal  
Les journaux sont écrits pour bloquer des objets blob dans le conteneur ou le répertoire virtuel spécifié par le paramètre `ImportExportStatesPath`, que vous pouvez définir sur une opération `Put Job`. L’emplacement dans lequel les journaux sont écrits dépend de la façon dont l’authentification est spécifiée pour le travail, ainsi que de la valeur spécifiée pour `ImportExportStatesPath`. L’authentification du travail peut être spécifiée via une clé de compte de stockage ou une SAP (signature d’accès partagé) de conteneur.  
  
Le nom du conteneur ou du répertoire virtuel peut être le nom par défaut `waimportexport` ou le nom d’un autre conteneur ou répertoire virtuel que vous spécifiez.  
  
Le tableau ci-dessous présente les options possibles :  
  
|Méthode d’authentification|Valeur de l’élément `ImportExportStatesPath`|Emplacement des objets blob de journal|  
|---------------------------|----------------------------------------------|---------------------------|  
|Clé du compte de stockage|Valeur par défaut|Un conteneur nommé `waimportexport`, qui est le conteneur par défaut. Par exemple :<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Clé du compte de stockage|Valeur spécifiée par l’utilisateur|Un conteneur nommé par l’utilisateur. Par exemple :<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|SAP de conteneur|Valeur par défaut|Un répertoire virtuel nommé `waimportexport`, qui est le nom par défaut, sous le conteneur spécifié dans la SAP.<br /><br /> Par exemple, si la SAP spécifiée pour le travail est `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, l’emplacement du journal serait alors `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|SAP de conteneur|Valeur spécifiée par l’utilisateur|Un répertoire virtuel nommé par l’utilisateur, sous le conteneur spécifié dans la SAP.<br /><br /> Par exemple, si la SAP spécifiée pour le travail est `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` et le répertoire virtuel spécifié est nommé `mylogblobs`, l’emplacement du journal serait `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Vous pouvez récupérer l’URL de l’erreur et les journaux détaillés en appelant l’opération [Get Job](/rest/api/storageservices/importexport/Get-Job3). Les journaux sont disponibles dès que le traitement du lecteur est terminé.  
  
## <a name="log-file-format"></a>Format de fichier journal  
Le format de ces deux journaux est le même : un objet blob contenant des descriptions XML des événements qui se sont produits lors de la copie des objets blob entre le disque dur et le compte du client.  
  
Le journal détaillé contient des informations complètes sur l’état de l’opération de copie pour chaque objet blob (pour un travail d’importation) ou un fichier (pour un travail d’exportation), alors que le journal d’erreurs contient uniquement les informations des objets blob ou fichiers qui ont rencontré des erreurs lors du travail d’importation ou d’exportation.  
  
Le format du journal détaillé est illustré ci-dessous. Le journal d’erreurs a la même structure mais il exclut les opérations réussies.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

Le tableau suivant décrit les éléments du fichier journal.  
  
|Élément XML|Type|Description|  
|-----------------|----------|-----------------|  
|`DriveLog`|Élément XML|Représente un journal de lecteur.|  
|`Version`|Attribut, Chaîne|Version du format du journal.|  
|`DriveId`|String|Numéro de série du matériel du lecteur.|  
|`Status`|String|État de traitement du lecteur. Pour plus d’informations, consultez le tableau `Drive Status Codes` ci-après.|  
|`Blob`|Élément XML imbriqué|Représente un objet blob.|  
|`Blob/BlobPath`|String|URI de l’objet blob.|  
|`Blob/FilePath`|String|Chemin relatif d’accès au fichier sur le lecteur.|  
|`Blob/Snapshot`|DateTime|Version de l’instantané de l’objet blob, pour un travail d’exportation.|  
|`Blob/Length`|Entier |Longueur totale de l’objet blob en octets.|  
|`Blob/LastModified`|DateTime|Date/heure de dernière modification de l’objet blob, pour un travail d’exportation.|  
|`Blob/ImportDisposition`|String|Disposition d’importation de l’objet blob, pour un travail d’exportation uniquement.|  
|`Blob/ImportDisposition/@Status`|Attribut, Chaîne|État de la disposition d’importation.|  
|`PageRangeList`|Élément XML imbriqué|Représente une liste de plages de pages pour un objet blob de pages.|  
|`PageRange`|Élément XML|Représente une plage de pages.|  
|`PageRange/@Offset`|Attribut, Entier|Décalage de début de la plage de pages dans l’objet blob.|  
|`PageRange/@Length`|Attribut, Entier|Longueur en octets de la plage de pages.|  
|`PageRange/@Hash`|Attribut, Chaîne|Hachage MD5 encodé en Base16 de la plage de pages.|  
|`PageRange/@Status`|Attribut, Chaîne|État du traitement de la plage de pages.|  
|`BlockList`|Élément XML imbriqué|Représente une liste de blocs pour un objet blob de blocs.|  
|`Block`|Élément XML|Représente un bloc.|  
|`Block/@Offset`|Attribut, Entier|Décalage de début du bloc dans l’objet blob.|  
|`Block/@Length`|Attribut, Entier|Longueur du bloc en octets.|  
|`Block/@Id`|Attribut, Chaîne|ID du bloc.|  
|`Block/@Hash`|Attribut, Chaîne|Hachage MD5 encodé en Base16 du bloc.|  
|`Block/@Status`|Attribut, Chaîne|État du traitement du bloc.|  
|`Metadata`|Élément XML imbriqué|Représente les métadonnées de l’objet blob.|  
|`Metadata/@Status`|Attribut, Chaîne|État de traitement des métadonnées de l’objet blob.|  
|`Metadata/GlobalPath`|String|Chemin relatif d’accès au fichier de métadonnées global.|  
|`Metadata/GlobalPath/@Hash`|Attribut, Chaîne|Hachage MD5 encodé en Base16 du fichier de métadonnées global.|  
|`Metadata/Path`|String|Chemin relatif d’accès au fichier de métadonnées.|  
|`Metadata/Path/@Hash`|Attribut, Chaîne|Hachage MD5 encodé en Base16 du fichier de métadonnées.|  
|`Properties`|Élément XML imbriqué|Représente les propriétés de l’objet blob.|  
|`Properties/@Status`|Attribut, Chaîne|État de traitement des propriétés de l’objet blob, par exemple, fichier introuvable, terminé.|  
|`Properties/GlobalPath`|String|Chemin relatif d’accès au fichier de propriétés global.|  
|`Properties/GlobalPath/@Hash`|Attribut, Chaîne|Hachage MD5 encodé en Base16 du fichier de propriétés global.|  
|`Properties/Path`|String|Chemin relatif d’accès au fichier de propriétés.|  
|`Properties/Path/@Hash`|Attribut, Chaîne|Hachage MD5 encodé en Base16 du fichier de propriétés.|  
|`Blob/Status`|String|État du traitement de l’objet blob.|  
  
### <a name="drive-status-codes"></a>Codes d’état du lecteur  
Le tableau suivant répertorie les codes d’état pour le traitement d’un lecteur.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|`Completed`|Le traitement du lecteur s’est terminé sans erreurs.|  
|`CompletedWithWarnings`|Le lecteur a terminé le traitement avec des avertissements dans un ou plusieurs objets blob selon les dispositions d’importation spécifiées pour les objets blob.|  
|`CompletedWithErrors`|Le lecteur a terminé avec des erreurs dans un ou plusieurs objets blob ou segments.|  
|`DiskNotFound`|Aucun disque ne se trouve sur le lecteur.|  
|`VolumeNotNtfs`|Le premier volume de données sur le disque n’est pas au format NTFS.|  
|`DiskOperationFailed`|Une erreur inconnue s’est produite lors de l’exécution d’opérations sur le lecteur.|  
|`BitLockerVolumeNotFound`|Aucun volume BitLocker chiffrable n’a été trouvé.|  
|`BitLockerNotActivated`|BitLocker n’est pas activé sur le volume.|  
|`BitLockerProtectorNotFound`|Le protecteur de clé de mot de passe numérique n’existe pas sur le volume.|  
|`BitLockerKeyInvalid`|Le mot de passe numérique fourni ne peut pas déverrouiller le volume.|  
|`BitLockerUnlockVolumeFailed`|Une erreur inconnue s’est produite lors de la tentative de déverrouillage du volume.|  
|`BitLockerFailed`|Une erreur inconnue s’est produite lors de l’exécution d’opérations BitLocker.|  
|`ManifestNameInvalid`|Le nom du fichier manifeste n’est pas valide.|  
|`ManifestNameTooLong`|Le nom du fichier manifeste est trop long.|  
|`ManifestNotFound`|Le fichier manifeste est introuvable.|  
|`ManifestAccessDenied`|L’accès au fichier manifeste est refusé.|  
|`ManifestCorrupted`|Le fichier manifeste est endommagé (le contenu ne correspond pas à son hachage).|  
|`ManifestFormatInvalid`|Le contenu du manifeste n’est pas conforme au format requis.|  
|`ManifestDriveIdMismatch`|L’ID du lecteur dans le fichier manifeste ne correspond pas à celui lu à partir du lecteur.|  
|`ReadManifestFailed`|Un erreur d’E/S du disque s’est produite lors de la lecture à partir du manifeste.|  
|`BlobListFormatInvalid`|L’objet blob de la liste d’objets blob d’exportation n’est pas conforme au format requis.|  
|`BlobRequestForbidden`|L’accès aux objets blob dans le compte de stockage est interdit. Cela peut être dû à une clé de compte de stockage ou une SAP de conteneur non valide.|  
|`InternalError`|Une erreur interne s'est produite lors du traitement du lecteur.|  
  
### <a name="blob-status-codes"></a>Codes d’état de l’objet blob  
Le tableau suivant répertorie les codes d’état pour le traitement d’un objet blob.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|`Completed`|Le traitement de l’objet blob s’est terminé sans erreurs.|  
|`CompletedWithErrors`|Le traitement de l’objet blob s’est terminé avec des erreurs dans une ou plusieurs plages de pages ou blocs, métadonnées ou propriétés.|  
|`FileNameInvalid`|Le nom du fichier n'est pas valide.|  
|`FileNameTooLong`|Le nom du fichier est trop long.|  
|`FileNotFound`|Le fichier est introuvable.|  
|`FileAccessDenied`|L’accès au fichier est refusé.|  
|`BlobRequestFailed`|La demande de service Blob pour accéder à l’objet blob a échoué.|  
|`BlobRequestForbidden`|La demande de service Blob pour accéder à l’objet blob est interdite. Cela peut être dû à une clé de compte de stockage ou une SAP de conteneur non valide.|  
|`RenameFailed`|Impossible de renommer l’objet blob (pour un travail d’importation) ou le fichier (pour un travail d’exportation).|  
|`BlobUnexpectedChange`|Une modification inattendue s’est produite avec l’objet blob (pour un travail d’exportation).|  
|`LeasePresent`|Il existe un bail sur l’objet blob.|  
|`IOFailed`|Une erreur d’E/S du disque ou réseau s’est produite lors du traitement de l’objet blob.|  
|`Failed`|Une erreur inconnue s’est produite lors du traitement de l’objet blob.|  
  
### <a name="import-disposition-status-codes"></a>Codes d’état de disposition d’importation  
Le tableau suivant répertorie les codes d’état de résolution d’une disposition d’importation.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|`Created`|L’objet blob a été créé.|  
|`Renamed`|L’objet blob a été renommé en fonction de la disposition d’importation de changement de nom. L’élément `Blob/BlobPath` contient l’URI de l’objet blob renommé.|  
|`Skipped`|L’objet blob a été ignoré en fonction de la disposition d’importation `no-overwrite`.|  
|`Overwritten`|L’objet blob a remplacé un objet blob existant en fonction de la disposition d’importation `overwrite`.|  
|`Cancelled`|Une erreur précédente a arrêté le traitement de la disposition d’importation.|  
  
### <a name="page-rangeblock-status-codes"></a>Codes d’état de plage de pages/de bloc  
Le tableau suivant répertorie les codes d’état pour le traitement d’une plage de pages ou d’un bloc.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|`Completed`|Le traitement de la plage de pages ou du bloc s’est terminé sans erreurs.|  
|`Committed`|Le bloc a été validé, mais pas dans la liste de blocs complète car d’autres blocs ont échoué, ou la liste de blocs complète elle-même a échoué.|  
|`Uncommitted`|Le bloc est téléchargé mais non validé.|  
|`Corrupted`|La plage de pages ou le bloc est endommagé (le contenu ne correspond pas à son hachage).|  
|`FileUnexpectedEnd`|Une fin de fichier inattendue a été rencontrée.|  
|`BlobUnexpectedEnd`|Une fin d’objet blob inattendue a été rencontrée.|  
|`BlobRequestFailed`|La demande de service Blob pour accéder à la plage de pages ou au bloc a échoué.|  
|`IOFailed`|Une erreur d’E/S du disque ou réseau s’est produite lors du traitement de la plage de pages ou du bloc.|  
|`Failed`|Une erreur inconnue s’est produite lors du traitement de la plage de pages ou du bloc.|  
|`Cancelled`|Une erreur précédente a arrêté le traitement de la plage de pages ou du bloc.|  
  
### <a name="metadata-status-codes"></a>Codes d’état des métadonnées  
Le tableau suivant répertorie les codes d’état pour le traitement des métadonnées d’objet blob.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|`Completed`|Le traitement des métadonnées s’est terminé sans erreurs.|  
|`FileNameInvalid`|Le nom du fichier de métadonnées n'est pas valide.|  
|`FileNameTooLong`|Le nom du fichier de métadonnées est trop long.|  
|`FileNotFound`|Le fichier de métadonnées est introuvable.|  
|`FileAccessDenied`|L’accès au fichier de métadonnées est refusé.|  
|`Corrupted`|Le fichier de métadonnées est endommagé (le contenu ne correspond pas à son hachage).|  
|`XmlReadFailed`|Le contenu des métadonnées n’est pas conforme au format requis.|  
|`XmlWriteFailed`|L’écriture des métadonnées XML a échoué.|  
|`BlobRequestFailed`|La demande de service Blob pour accéder aux métadonnées a échoué.|  
|`IOFailed`|Une erreur d’E/S du disque ou réseau s’est produite lors du traitement des métadonnées.|  
|`Failed`|Une erreur inconnue s’est produite lors du traitement des métadonnées.|  
|`Cancelled`|Une erreur précédente a arrêté le traitement des métadonnées.|  
  
### <a name="properties-status-codes"></a>Codes d’état des propriétés  
Le tableau suivant répertorie les codes d’état pour le traitement des propriétés d’objet blob.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|`Completed`|Le traitement des propriétés s’est terminé sans erreurs.|  
|`FileNameInvalid`|Le nom du fichier de propriétés n’est pas valide.|  
|`FileNameTooLong`|Le nom du fichier de propriétés est trop long.|  
|`FileNotFound`|Le fichier de propriétés est introuvable.|  
|`FileAccessDenied`|L’accès au fichier de propriétés est refusé.|  
|`Corrupted`|Le fichier de propriétés est endommagé (le contenu ne correspond pas à son hachage).|  
|`XmlReadFailed`|Le contenu des propriétés n’est pas conforme au format requis.|  
|`XmlWriteFailed`|L’écriture des propriétés XML a échoué.|  
|`BlobRequestFailed`|La demande de service Blob pour accéder aux propriétés a échoué.|  
|`IOFailed`|Une erreur d’E/S du disque ou réseau s’est produite lors du traitement des propriétés.|  
|`Failed`|Une erreur inconnue s’est produite lors du traitement des propriétés.|  
|`Cancelled`|Une erreur précédente a arrêté le traitement des propriétés.|  
  
## <a name="sample-logs"></a>Exemples de journaux  
Vous trouverez ci-dessous un exemple de journal détaillé.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Le journal d’erreurs correspondant est illustré ci-dessous.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Le journal d’erreurs suivant pour un travail d’importation contient une erreur sur un fichier introuvable sur le lecteur d’importation. Notez que l’état des composants suivants est `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

Le journal d’erreurs suivant pour un travail d’exportation indique que le contenu de l’objet blob a été correctement écrit sur le lecteur, mais qu’une erreur s’est produite lors de l’exportation des propriétés de l’objet blob.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="see-also"></a>Voir aussi  
[API REST d’importation/exportation de stockage](/rest/api/storageservices/importexport/Storage-Import-Export-Service-REST-API-Reference)



<!--HONumber=Dec16_HO2-->


