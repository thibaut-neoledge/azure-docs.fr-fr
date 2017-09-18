---
title: Analyser et traiter des documents JSON avec Hive dans HDInsight | Microsoft Docs
description: "Découvrez comment utiliser des documents JSON et les analyser avec Hive dans HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: ee7d40d2ff0ae1ac10b54f4c1f1dd704a70eb70c
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Traitement et analyse des documents JSON avec Hive dans HDInsight

Apprenez à traiter et analyser les fichiers JSON à l’aide de Hive dans HDInsight. Le document JSON suivant sera utilisé dans le tutoriel :

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

Le fichier se trouve à l’emplacement suivant : wasb://processjson@hditutorialdata.blob.core.windows.net/. Pour plus d’informations sur l’utilisation du stockage Blob Azure avec HDInsight, consultez la page [Utilisation du stockage d’objets blob Azure compatibles avec HDFS avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md). Vous pouvez copier le fichier dans le conteneur par défaut de votre cluster.

Dans ce tutoriel, vous allez utiliser la console Hive.  Pour obtenir des instructions sur l’ouverture de la console Hive, consultez la page [Utilisation de Hive avec Hadoop sur HDInsight avec le Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Aplatir des documents JSON
Les méthodes répertoriées dans la section suivante nécessitent que le document JSON n’occupe qu’une seule ligne. Par conséquent, vous devez aplatir le document JSON en une chaîne. Si votre document JSON est déjà aplati et que l’ensemble du document tient sur une ligne, vous pouvez ignorer cette étape et passer directement à la section suivante sur l’analyse des données JSON.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

Le fichier JSON brut se trouve à l’emplacement suivant : **wasb://processjson@hditutorialdata.blob.core.windows.net/**. La table Hive *StudentsRaw* pointe vers le document JSON brut non aplati.

La table Hive *StudentsOneLine* stocke les données dans le système de fichiers HDInsight par défaut sous le chemin d’accès */json/students/*.

L’instruction INSERT remplit la table StudentOneLine avec les données JSON aplaties.

L’instruction SELECT retourne seulement 1 ligne.

Voici la sortie de l’instruction SELECT :

![Aplatissage du document JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analyser les documents JSON dans Hive
Hive propose trois mécanismes différents pour exécuter des requêtes sur des documents JSON :

* Utilisation de la fonction définie par l’utilisateur GET\_JSON\_OBJECT
* Utilisation de la fonction UDF JSON_TUPLE
* Utilisation d’un SerDe personnalisé
* Écriture de votre propre fonction UDF à l’aide de Python ou d’autres langages Consultez [cet article][hdinsight-python] consacré à l’exécution de votre propre code Python avec Hive.

### <a name="use-the-getjsonobject-udf"></a>Utiliser la fonction UDF GET\_JSON_OBJECT
Hive intègre une fonction UDF appelée [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) qui permet d’exécuter des requêtes sur un document JSON pendant l’exécution. Cette méthode accepte deux arguments : d’une part, le nom de la table et le nom de la méthode contenant le document JSON aplati et, d’autre part, le champ JSON à analyser. Prenons un exemple pour examiner de plus près cette fonction UDF.

Obtenir le prénom et le nom de chaque élève

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Voici la sortie de cette requête dans la fenêtre de console.

![Fonction UDF get_json_object][image-hdi-hivejson-getjsonobject]

La fonction UDF get-json_object présente quelques limitations.

* Étant donné que chaque champ de la requête requiert une nouvelle analyse de la requête, cela influe sur les performances.
* GET\_JSON_OBJECT() retourne une représentation sous forme de chaîne d’un tableau. Pour convertir cette dernière en tableau Hive, vous devez utiliser des expressions régulières pour remplacer les crochets « [ » et « ] », puis appeler split pour obtenir le tableau.

C’est pourquoi le wiki Hive recommande l’utilisation de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utiliser la fonction UDF JSON_TUPLE
L’autre fonction UDF fournie par Hive, intitulée [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), est plus performante que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Cette méthode, qui accepte un ensemble de clés et une chaîne JSON, retourne un tuple de valeurs en utilisant une seule fonction. La requête suivante renvoie l’ID de l'étudiant et la qualité du document JSON :

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Sortie de ce script dans la console Hive :

![Fonction UDF json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE utilise la syntaxe [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) dans Hive, ce qui permet à json\_tuple de créer une table virtuelle en appliquant la fonction UDT à chaque ligne de la table d’origine.  Les documents JSON deviennent trop complexes en raison de l’utilisation répétée de LATERAL VIEW. En outre, JSON_TUPLE ne peut pas gérer les documents JSON imbriqués.

### <a name="use-custom-serde"></a>Utiliser un SerDe personnalisé
SerDe est le meilleur choix pour l’analyse des documents JSON imbriqués ; il vous permet de définir le schéma JSON et de l’utiliser pour analyser les documents. Pour obtenir des instructions, consultez [Guide pratique pour utiliser un SerDe JSON personnalisé avec Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Résumé
En conclusion, le type d'opérateur JSON que vous choisissez dans Hive dépend de votre scénario. Si vous possédez un document JSON simple et que votre recherche ne porte que sur un seul champ, vous pouvez utiliser la fonction UDF Hive get\_json\_object. Si la recherche porte sur plusieurs clés, vous pouvez utiliser json_tuple. Enfin, si vous disposez d'un document imbriqué, il est recommandé d'utiliser le SerDe JSON.

## <a name="next-steps"></a>Étapes suivantes

Autres articles associés :

* [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md)
* [Analyse des données sur les retards de vol avec Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analyse des données Twitter avec Hive dans HDInsight](hdinsight-analyze-twitter-data.md)
* [Exécution d’une tâche Hadoop avec Azure Cosmos DB et HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

