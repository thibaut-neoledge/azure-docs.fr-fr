---
title: "Extension des scripts U-SQL à l’aide de code R dans Azure Data Lake Analytics | Microsoft Docs"
description: "Découvrez comment exécuter un code R dans des scripts U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Tutoriel : Get started with extending U-SQL with R (Bien démarrer avec l’extension de U-SQL avec R)

L’exemple suivant illustre les étapes de base pour déployer un code R :
* Utiliser l’instruction REFERENCE ASSEMBLY pour activer les extensions R pour le script U-SQL.
* Utiliser l’opération REDUCE pour partitionner les données d’entrée sur une clé.
* Les extensions R pour U-SQL comprennent un réducteur intégré (Extension.R.Reducer) qui exécute le code R sur chaque vertex affecté au réducteur. 
* Utilisation de trames de données nommées dédiées, respectivement intitulées inputFromUSQL et outputToUSQL, pour transférer des données entre USQL et R. Les noms des identificateurs des trames de données d’entrée et de sortie sont fixes. Autrement dit, les utilisateurs ne peuvent pas modifier les noms prédéfinis des identificateurs des trames de données d’entrée et de sortie.


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>Intégration de R à U-SQL

### <a name="datatypes"></a>Types de données
* Les colonnes de chaîne et numériques de U-SQL sont converties en l’état entre une trame de données R et U-SQL [types pris en charge : double, chaîne, booléen, entier, octets].
* Le type de données facteur n’est pas pris en charge dans U-SQL.
* byte[] doit être sérialisé sous forme de chaîne codée en base64.
* Les chaînes U-SQL peuvent être converties en facteurs dans le code R lorsque U-SQL crée une trame de données d’entrée R ou en définissant le paramètre du réducteur stringsAsFactors sur true.

### <a name="schemas"></a>Schémas
* Les jeux de données U-SQL ne peuvent pas avoir de noms de colonnes en double.
* Les noms de colonnes des jeux de données U-SQL doivent être des chaînes.
* Les noms de colonnes doivent être identiques dans U-SQL et dans les scripts R.
* Les colonnes en lecture seule ne peuvent pas faire partie de la trame de données de sortie. Car les colonnes en lecture seule sont automatiquement réinjectées dans la table U-SQL si elles font partie du schéma de sortie de l’opérateur défini par l’utilisateur (UDO).

## <a name="next-steps"></a>Étapes suivantes
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilisation des fonctions U-SQL dans les travaux Analytique Data Lake Azure](data-lake-analytics-use-window-functions.md)

