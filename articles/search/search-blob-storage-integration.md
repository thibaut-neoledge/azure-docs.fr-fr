---
title: "Ajout de Recherche Azure à Stockage Blob | Microsoft Docs"
description: "Créer un index dans le code à l’aide de l’API REST HTTP de la Recherche Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 15469e8a2d28bdf00d6e8d8c9f823c51975ee90e
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Recherche dans le Stockage Blob avec la Recherche Azure

La recherche dans les différents types de contenu enregistrés dans le Stockage Blob Azure peut constituer un problème difficile à résoudre. Toutefois, vous pouvez indexer et rechercher le contenu de vos objets blob en quelques clics à l’aide de la Recherche Azure. La recherche dans le Stockage Blob nécessite d’approvisionner un service Recherche Azure. Les différentes limites de service et les niveaux tarifaires de Recherche Azure sont indiqués sur la [page de tarification](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Présentation de Recherche Azure
[Recherche Azure](https://aka.ms/whatisazsearch) est un service de recherche qui permet facilement aux développeurs d’ajouter des expériences de recherche de texte intégral fiables aux applications web et mobiles. En tant que service, Recherche Azure élimine la nécessité de gérer une infrastructure de recherche, tout en offrant un [SLA garantissant un temps d’activité de 99,9 %](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Indexation et recherche : formats de documents d’entreprise pris en charge
Du fait de la prise en charge de l’[extraction de documents](https://aka.ms/azsblobindexer) dans le Stockage Blob Azure, vous pouvez indexer le contenu suivant :

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

En extrayant le texte et les métadonnées de ces types de fichiers, vous pouvez effectuer une recherche dans plusieurs formats de fichiers avec une même requête. 

## <a name="search-through-your-blob-metadata"></a>Effectuer des recherches dans vos métadonnées d’objets blob
Un scénario courant qui facilite le tri dans les objets blob comportant tout type de contenu est celui qui consiste à indexer les métadonnées personnalisées et les propriétés système pour chaque objet blob. De cette façon, les informations de tous les objets blob sont indexées indépendamment du type de document. Vous pouvez alors effectuer un tri, un filtrage et une facette dans l’ensemble du contenu du stockage Blob.

[En savoir que plus sur l’indexation des métadonnées d’objets blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Recherche d’images
La recherche de texte intégral de Recherche Azure, la navigation à facettes et les capacités de tri peuvent désormais être appliquées aux métadonnées des images stockées dans les objets blob.

Si ces images sont traitées au préalable à l’aide de l’[API Vision par ordinateur](https://www.microsoft.com/cognitive-services/computer-vision-api) à partir de Microsoft Cognitive Services, il est possible d’indexer le contenu visuel de chaque image, y compris la reconnaissance de l’écriture manuscrite et la reconnaissance optique de caractères. Nous travaillons sur l’ajout de la reconnaissance optique de caractères et d’autres fonctionnalités de traitement d’images directement dans Recherche Azure. Si ces fonctionnalités vous intéressent, formulez une demande via [UserVoice](https://aka.ms/azsuv) ou par [e-mail](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indexation et recherche à l’aide des objets blob JSON
Recherche Azure peut être configuré pour extraire le contenu structuré des objets blob qui contiennent des objets JSON. Recherche Azure peut lire les objets blob JSON et analyser le contenu structuré dans les champs adaptés du document Recherche Azure. Recherche Azure peut également extraire les objets blob contenant des objets JSON et mapper chaque élément avec un document Recherche Azure différent.

L’analyse JSON n’est actuellement pas configurable via le portail. [En savoir plus sur l’analyse des objets JSON dans Recherche Azure.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Démarrage rapide
Il est possible d’ajouter directement Recherche Azure à des objets Blob, à partir de la page Stockage Blob du portail.

![](./media/search-blob-storage-integration/blob-blade.png)

Cliquez sur **Ajouter Recherche Azure** pour lancer un flux dans lequel vous pouvez sélectionner un service Recherche Azure existant ou créer un nouveau service. Si vous créez un nouveau service, vous quittez l’expérience portail de votre compte Stockage. Vous pouvez revenir à la page Stockage du portail, sélectionner à nouveau l’option **Ajouter Recherche Azure**, puis sélectionner le service existant.

### <a name="next-steps"></a>Étapes suivantes
Consultez la [documentation](https://aka.ms/azsblobindexer) pour en savoir plus sur l’indexeur d’objets blob Recherche Azure.

