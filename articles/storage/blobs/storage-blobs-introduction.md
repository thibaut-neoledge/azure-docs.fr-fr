---
title: "Présentation du Stockage Blob Azure | Microsoft Docs"
description: "Présentation du Stockage Blob Azure"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: 6059ce809e7a8b2115e391d9db5b5b0a8626109e
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="introduction-to-blob-storage"></a>Présentation du Stockage Blob

Le stockage d’objets blob Azure est un service permettant de stocker de gros volumes de données d’objets non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Vous pouvez utiliser le stockage d'objets blob pour exposer les données publiquement dans le monde ou pour le stockage privé de données d'applications.

Voici quelques utilisations courantes du stockage d’objets blob :

* Mise à disposition d’images ou de documents directement dans un navigateur
* Stockage de fichiers pour un accès distribué
* Diffusion en continu de vidéo et d’audio
* Stockage de données pour la sauvegarde et la restauration, la récupération d’urgence et l’archivage
* Stockage des données pour l’analyse par un service local ou hébergé par Azure

## <a name="blob-service-concepts"></a>Concepts du service BLOB

Le service d’objets blob contient les composants suivants :

![Architecture d’objets blob](./media/storage-blobs-introduction/blob1.png)

* **Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Ce compte de stockage peut être un **compte de stockage à usage général** ou un **compte de stockage d’objets blob** spécialisé pour le stockage d’objets/objets blob. Pour plus d’informations, voir [À propos des comptes de stockage Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* **Conteneur :** un conteneur regroupe un ensemble d'objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d’objets blob. Notez que le nom du conteneur doit être en minuscules.

* **Objet blob :** fichier de tout type et de toute taille. Azure Storage propose trois types d’objets blob : les objets blob de blocs, les objets blob d’ajouts et les objets blob de pages.
  
    Les *objets blob de blocs* sont parfaits pour le stockage des fichiers texte ou binaires, tels que les documents et les fichiers multimédias. Les *objets blob d’ajout* sont similaires aux objets blob de blocs dans la mesure où ils sont composés de blocs. Cependant, ils sont optimisés pour les opérations d’ajouts et sont donc utiles pour les scénarios de journalisation. Un objet blob de blocs ou d’ajouts peut contenir jusqu’à 50 000 blocs de 100 Mo chacun, soit une taille totale légèrement supérieure à 4,75 Go (100 Mo x 50 000). Un objet blob d’ajout peut contenir jusqu’à 50 000 blocs de 4 Mo chacun, soit une taille totale légèrement supérieure à 195 Go (4 Mo x 50 000).
  
    La taille maximale des *objets blob de pages* peut atteindre 8 To. Ces objets sont plus efficaces pour les opérations fréquentes de lecture/écriture. Azure Virtual Machines utilise des objets blob de pages comme disques de données et disques du système d’exploitation.
  
    Pour plus de détails sur l’affectation de noms aux conteneurs et objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Prise en main du Stockage Blob avec .NET](storage-dotnet-how-to-use-blobs.md)
* [Exemples relatifs au service Stockage Azure avec .NET](../common/storage-samples-dotnet.md)
* [Exemples relatifs au service Stockage Azure avec Java](../common/storage-samples-java.md)
