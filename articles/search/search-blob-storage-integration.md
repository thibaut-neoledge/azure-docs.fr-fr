---
title: "Ajout de Recherche Azure à Stockage Blob | Microsoft Docs"
description: "Créer un index dans le code à l’aide de l’API REST HTTP d’Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Recherche dans le Stockage Blob avec la Recherche Azure

La recherche dans les différents types de contenu enregistrés dans le Stockage Blob Azure peut constituer un problème difficile à résoudre. Toutefois, vous pouvez indexer et rechercher le contenu de vos objets blob en quelques clics à l’aide de la Recherche Azure. La recherche dans le Stockage Blob nécessite d’approvisionner un service Recherche Azure. Les différentes limites de service et les niveaux de tarification de Recherche Azure sont indiqués sur la [page de tarification](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Présentation d’Azure Search
[Recherche Azure](https://aka.ms/whatisazsearch) est une solution de recherche qui permet aux développeurs d’ajouter des expériences de recherche de texte intégral fiables pour les applications web et mobiles. Avec le service Recherche Azure il n’est plus nécessaire de gérer une infrastructure de recherche. De plus, vous profitez d’une offre de [SLA garantissant un temps d’activité de 99,9 %](https://aka.ms/azuresearchsla).

Avec prise en charge avancée pour 56 langues, Recherche Azure peut analyser votre contenu et gérer intelligemment les constructions spécifiques à chaque langue. Recherche Azure fournit également les outils nécessaires pour créer une riche expérience de recherche. Les fonctions, comme la navigation à facettes, les suggestions de recherche en saisie semi-automatique et les correspondances, sont faciles à ajouter aux interfaces utilisateurs avec Recherche Azure. Pour en savoir plus sur les fonctionnalités de Recherche Azure, consultez la [documentation](https://aka.ms/azsearchdocs) sur le sujet.

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>Parcourez le contenu et les formats des documents d’entreprise.
Recherche Azure prend en charge [l’extraction de documents](https://aka.ms/azsblobindexer) dans Stockage Blob Azure et peut indexer le contenu d’un grand nombre de types de fichiers stockés dans des objets blob :
- PDF
- Microsoft Office : DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (e-mails Outlook)
- HTML
- Fichiers de texte brut

Grâce à l’extraction du texte et des métadonnées de ces types de fichiers, vous pouvez effectuer facilement une recherche dans plusieurs formats de fichiers, via une simple requête, pour trouver les documents les plus utiles, quel qu’en soit le type. En indexant le contenu et les métadonnées des documents Microsoft Office, des fichiers PDF et des courriers électroniques, il est possible de créer une solution de gestion de contenu d’entreprise fiable à l’aide du Stockage Blob et de Recherche Azure.

## <a name="search-through-your-blob-metadata"></a>Effectuer des recherches dans vos métadonnées d’objets blob
Voici un exemple de scénario courant permettant de faire une recherche dans des objets blob avec du contenu de tout type : il suffit d’indexer les métadonnées d’objets blob personnalisées définies par l’utilisateur, ainsi que les propriétés système pour chacun de vos objets blob. De cette façon, les informations pour chaque objet blob sont indexées quel que soit le type de document dans l’objet blob. Ainsi, vous pouvez facilement trier et définir des propriétés de facette pour l’ensemble de votre contenu Stockage Blob.

[En savoir que plus sur l’indexation des métadonnées d’objets blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Recherche d’images
La recherche de texte intégral de Recherche Azure, la navigation à facettes et les capacités de tri peuvent désormais être appliquées aux métadonnées des images stockées dans les objets blob.

Si ces images sont traitées au préalable à l’aide de l’[API Vision par ordinateur](https://www.microsoft.com/cognitive-services/computer-vision-api) à partir de Microsoft Cognitive Services, il est possible d’indexer le contenu visuel de chaque image, y compris la reconnaissance de l’écriture manuscrite et la reconnaissance optique de caractères. Nous travaillons sur l’ajout de la reconnaissance optique de caractères et d’autres fonctionnalités de traitement d’image directement dans Recherche Azure. Si ces fonctionnalités vous intéressent, veuillez envoyer une demande via[UserVoice](https://aka.ms/azsuv) ou par [e-mail](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indexation et recherche à l’aide des objets blob JSON
Recherche Azure peut être configuré pour extraire le contenu structuré des objets blob qui contiennent des objets JSON. Recherche Azure peut lire les objets blob JSON et analyser le contenu structuré dans les champs adaptés du document Recherche Azure. Recherche Azure peut également extraire les objets blob contenant des objets JSON et mapper chaque élément avec un document Recherche Azure différent.

Notez que l’analyse des objets JSON n’est pas actuellement configurable via le portail. [En savoir plus sur l’analyse des objets JSON dans Recherche Azure.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Démarrage rapide
Il est possible d’ajouter directement Recherche Azure dans des objets Blob, à partir du panneau Stockage Blob sur le portail.

![](./media/search-blob-storage-integration/blob-blade.png)

Cliquez sur l’option « Ajouter Recherche Azure » pour lancer un flux dans lequel vous pouvez sélectionner un service Recherche Azure existant ou créer un nouveau service. Si vous créez un nouveau service, vous quitterez l’expérience portail de votre compte Stockage. Vous devrez accéder à nouveau au panneau du portail Stockage et resélectionner l’option « Ajouter Recherche Azure », puis sélectionner le service existant.

### <a name="next-steps"></a>Étapes suivantes
Consultez la [documentation](https://aka.ms/azsblobindexer) pour en savoir plus sur l’indexeur d’objets blob Recherche Azure.

