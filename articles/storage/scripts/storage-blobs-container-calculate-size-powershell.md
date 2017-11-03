---
title: "Exemple de script Azure PowerShell - Calculer la taille d’un conteneur d’objets blob | Microsoft Docs"
description: "Calculez la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles de chacun de ses objets blob."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calculer la taille totale d’un conteneur de stockage d’objets blob

Ce script calcule la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles des objets blob inclus dans le conteneur.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Comprendre la taille d’un conteneur de stockage d’objets blob

La taille totale d’un conteneur de stockage d’objets blob comprend la taille du conteneur lui-même ainsi que celle de tous les objets blob qu’il contient.

La section suivante décrit comment calculer la capacité de stockage pour les conteneurs d’objets blob et les objets blob. Dans la suite de l’article, Len(X) désigne le nombre de caractères dans la chaîne.

### <a name="blob-containers"></a>Conteneurs d’objets blob

La quantité de stockage utilisée par le conteneur d’objets blob peut être estimée de la manière suivante :

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Voici la répartition :
* 48 octets de charge utile pour chaque conteneur comprenant l’heure de dernière modification, les autorisations, les paramètres publics et des métadonnées système.
* Le nom du conteneur est stocké au format Unicode. Vous devez donc prendre le nombre de caractères et le multiplier par 2.
* Pour chaque métadonnée de conteneur d’objets blob stockée, nous stockons la longueur du nom (au format ASCII), ainsi que la longueur de la valeur de chaîne.
* Les 512 octets par identificateur de signature comprennent le nom de l’identificateur signé, l’heure de début, l’heure d’expiration et les autorisations.

### <a name="blobs"></a>Objets blob

La quantité de stockage utilisée par objet blob peut être estimée de la manière suivante :

* Objet blob de blocs (objet blob de base ou instantané)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* Objet blob de pages (objet blob de base ou instantané)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

Voici la répartition :

* 124 octets de charge utile pour l’objet blob comprenant l’heure de dernière modification, la taille, le contrôle du cache, le type de contenu, la langue du contenu, l’encodage du contenu, le MD5 contenu, les autorisations, les informations sur l’instantané, le bail et des métadonnées système.
* Le nom de l’objet blob est stocké au format Unicode. Vous devez donc prendre le nombre de caractères et le multiplier par 2.
* Pour chaque métadonnée stockée, nous stockons la longueur du nom (au format ASCII), ainsi que la longueur de la valeur de chaîne.
* Pour les objets blob de blocs
    * 8 octets pour la liste de blocs
    * Nombre de blocs multiplié par la taille de l’ID de bloc en octets
    * Plus la taille des données dans tous les blocs validés et non validés. Lorsque des instantanés sont utilisés, cette taille inclut uniquement les données uniques pour cet objet blob de base ou instantané. Si les blocs non validés ne sont pas utilisés après une semaine, ils seront placés dans le récupérateur de mémoire et ne seront alors plus comptabilisés dans la facturation.
* Pour les objets blob de pages
    * Nombre de plages de pages non consécutives comportant des données multiplié par 12 octets. Il s’agit du nombre de plages de pages uniques que vous voyez lors de l’appel de l’API GetPageRanges.
    * Plus la taille des données en octets de toutes les pages stockées. Lorsque des instantanés sont utilisés, cette taille inclut uniquement les pages uniques pour cet objet blob de base ou instantané comptabilisé.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la facturation du stockage Azure, consultez la page relative à la [facturation du stockage Windows Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour Stockage Azure](../blobs/storage-samples-blobs-powershell.md).
