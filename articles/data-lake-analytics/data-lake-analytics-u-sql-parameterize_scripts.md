---
title: "Définir les paramètres des scripts U-SQL dans Azure Data Lake Analytics | Microsoft Docs"
description: "Découvrez comment définir les paramètres des scripts U-SQL dans Azure Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>Définition des paramètres des scripts U-SQL

Lorsque vous utilisez des scripts SQL-U, il convient de les paramétrer. Ces paramètres permettent de conserver le même corps principal et de contrôler l’exécution du script en transmettant des valeurs de paramètre distinctes. Habituellement, un script traite les données dans une plage de temps définie et un développeur peut souhaiter définir la date de début et la date de fin du script. 

Les scripts sont paramétrables de deux manières différentes :
* Le script permet l’ajout sécurisé de paramètres au début du script. U-SQL prend en charge cette méthode.
* Une API de soumission de travail permet aux appelants d’envoyer des paramètres. U-SQL ne prend pas en charge cette méthode.

## <a name="example-scripts"></a>Exemples de scripts

Voici un script simple initial :

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
Dans ce scénario, nous paramétrons ``Region`` en ajoutant une instruction `DECLARE EXTERNAL`.

```
DECLARE EXTERNAL @TargetRegion = "en-us";

@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

Lorsqu’il s’exécute, ce script filtre par défaut uniquement les lignes de la région `en-us`. Jusqu’ici, nous avons défini un paramètre dans le script. Le mot clé `EXTERNAL` indique qu’une personne peut redéfinir la valeur `@TargetRegion` en ajoutant une instruction DECLARE sous la forme d’un préfixe pour définir le paramètre `@TargetRegion`.

Le script suivant montre à quoi ressemble le script paramétré lorsqu’une valeur spécifique vous a été fournie pour le paramètre. Ce script filtre maintenant les lignes correspondant à la région `en-gb`.

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>Étapes suivantes
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

