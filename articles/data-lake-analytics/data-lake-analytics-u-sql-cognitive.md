---
title: "Utilisation de fonctionnalités cognitives U-SQL dans Azure Data Lake Analytics | Microsoft Docs"
description: "Découvrez comment utiliser l’intelligence des fonctionnalités cognitives U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: a651fe045d7eb1265f698ebb89843fd4c2b1c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Didacticiel : Bien démarrer avec les capacités cognitives U-SQL

## <a name="overview"></a>Vue d'ensemble
Les fonctionnalités cognitives de U-SQL permettent aux développeurs d’utiliser l’intelligence PUT dans leurs programmes de Big Data. 

Les fonctionnalités cognitives suivantes sont disponibles :
* Acquisition d’images : Détecter les visages
* Acquisition d’images : Détecter les émotions
* Acquisition d’images : Détecter les objets (balisage)
* Acquisition d’images : OCR (reconnaissance optique de caractères)
* Texte : Extraction de phrases clés
* Texte : Analyse des sentiments

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Comment utiliser les fonctionnalités cognitives dans votre script U-SQL

Le processus général est simple :

* Utiliser l’instruction REFERENCE ASSEMBLY pour activer les fonctionnalités cognitives pour le script SQL-U
* Utiliser l’instruction PROCESS sur un ensemble de lignes d’entrée à l’aide d’un UDO cognitif pour générer une sortie d’ensemble de lignes

### <a name="detecting-objects-in-images"></a>Détection d’objets dans des images

L’exemple suivant montre comment utiliser les fonctionnalités cognitifs pour détecter des objets dans des images.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputers.Csv();
```
Pour plus d’exemples, consultez les **Exemples cognitifs/U-SQL** dans la section **Étapes suivantes**.

## <a name="next-steps"></a>Étapes suivantes
* [Exemples cognitifs/U-SQL](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilisation des fonctions U-SQL dans les travaux Analytique Data Lake Azure](data-lake-analytics-use-window-functions.md)
