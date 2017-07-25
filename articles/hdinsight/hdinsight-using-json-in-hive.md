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
ms.date: 04/26/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 034b61fdeb34c03bb4b58145f04138410e0d6fc6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# Traitement et analyse des documents JSON avec Hive dans HDInsight
<a id="process-and-analyze-json-documents-using-hive-in-hdinsight" class="xliff"></a>

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

Le fichier se trouve à l’emplacement suivant : wasbs://processjson@hditutorialdata.blob.core.windows.net/. Pour plus d’informations sur l’utilisation du stockage Blob Azure avec HDInsight, consultez la page [Utilisation du stockage d’objets blob Azure compatibles avec HDFS avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md). Vous pouvez copier le fichier dans le conteneur par défaut de votre cluster.

Dans ce tutoriel, vous allez utiliser la console Hive.  Pour obtenir des instructions sur l’ouverture de la console Hive, consultez la page [Utilisation de Hive avec Hadoop sur HDInsight avec le Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md).

## Aplatir des documents JSON
<a id="flatten-json-documents" class="xliff"></a>
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

Le fichier JSON brut se trouve à l’emplacement suivant : **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. La table Hive *StudentsRaw* pointe vers le document JSON brut non aplati.

La table Hive *StudentsOneLine* stocke les données dans le système de fichiers HDInsight par défaut sous le chemin d’accès */json/students/*.

L’instruction INSERT remplit la table StudentOneLine avec les données JSON aplaties.

L’instruction SELECT retourne seulement 1 ligne.

Voici la sortie de l’instruction SELECT :

![Aplatissage du document JSON][image-hdi-hivejson-flatten]

## Analyser les documents JSON dans Hive
<a id="analyze-json-documents-in-hive" class="xliff"></a>
Hive propose trois mécanismes différents pour exécuter des requêtes sur des documents JSON :

* utilisation de la fonction définie par l’utilisateur GET\_JSON\_OBJECT ;
* utilisation de la fonction UDF JSON_TUPLE ;
* utilisation d’un SerDe personnalisé ;
* écriture de votre propre fonction UDF à l’aide de Python ou d’autres langages. Consultez [cet article][hdinsight-python] consacré à l’exécution de votre propre code Python avec Hive.

### Utiliser la fonction UDF GET\_JSON_OBJECT
<a id="use-the-getjsonobject-udf" class="xliff"></a>
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

### Utiliser la fonction UDF JSON_TUPLE
<a id="use-the-jsontuple-udf" class="xliff"></a>
L’autre fonction UDF fournie par Hive, intitulée [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), est plus performante que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Cette méthode, qui accepte un ensemble de clés et une chaîne JSON, retourne un tuple de valeurs en utilisant une seule fonction. La requête suivante renvoie l’ID de l'étudiant et la qualité du document JSON :

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Sortie de ce script dans la console Hive :

![Fonction UDF json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE utilise la syntaxe [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) dans Hive, ce qui permet à json\_tuple de créer une table virtuelle en appliquant la fonction UDT à chaque ligne de la table d’origine.  Les documents JSON deviennent trop complexes en raison de l’utilisation répétée de LATERAL VIEW. En outre, JSON_TUPLE ne peut pas gérer les documents JSON imbriqués.

### Utiliser un SerDe personnalisé
<a id="use-custom-serde" class="xliff"></a>
SerDe est le meilleur choix pour l’analyse des documents JSON imbriqués ; il vous permet de définir le schéma JSON et de l’utiliser pour analyser les documents. Dans ce tutoriel, vous allez utiliser l’un des SerDe les plus populaires développés par [Roberto Congiu](https://github.com/rcongiu).

**Pour utiliser le SerDe personnalisé :**

1. Installez le [JDK 1.7.0_55 du Kit de développement SE Java 7u55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Si vous envisagez d’utiliser le déploiement Windows de HDInsight, choisissez la version Windows X64 du JDK.
   
   > [!WARNING]
   > Le JDK 1.8 ne fonctionne pas avec ce SerDe.
   > 
   > 
   
    Une fois l'installation terminée, ajoutez une nouvelle variable d’environnement utilisateur :
   
   1. Ouvrez **Afficher les paramètres système avancés** à partir de l’écran de Windows.
   2. Cliquez sur **Variables d’environnement**.  
   3. Ajoutez une nouvelle variable d’environnement **JAVA_HOME** pointant vers **C:\Program Files\Java\jdk1.7.0_55** ou vers l’emplacement où est installé votre JDK.
      
      ![Définition de valeurs de configuration correctes pour JDK][image-hdi-hivejson-jdk]
2. Installer [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)
   
    Ajoutez le dossier bin à votre chemin d’accès. Pour cela, accédez à Panneau de configuration --> Modifier les variables d’environnement système --> Variables d’environnement. La capture d’écran ci-dessous montre comment effectuer cette opération.
   
    ![Configuration de Maven][image-hdi-hivejson-maven]
3. Clonez le projet à partir du site github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) . Pour cela, cliquez sur le bouton « Download Zip » illustré dans la capture d’écran ci-dessous.
   
    ![Clonage du projet][image-hdi-hivejson-serde]

4: Accédez au dossier dans lequel vous avez téléchargé ce package, puis tapez « mvn package ». Cette action doit créer les fichiers jar nécessaires que vous pouvez ensuite copier dans le cluster.

5: Accédez au dossier cible sous le dossier racine dans lequel vous avez téléchargé le package. Téléchargez le fichier json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar dans le nœud principal de votre cluster. En général, je le mets dans le dossier binaire Hive (C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin) ou un dossier semblable.

6: À l’invite Hive, tapez «add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.ja». Étant donné que dans le cas présent, le fichier jar se trouve dans le dossier C:\apps\dist\hive-0.13.x\bin, je peux ajouter le jar directement avec le nom comme indiqué :

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

   ![Ajout de JAR à votre projet][image-hdi-hivejson-addjar]

Vous pouvez dorénavant utiliser le SerDe pour exécuter des requêtes sur le document JSON.

L’instruction suivante crée une table avec un schéma défini :

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Pour répertorier le prénom et le nom de l’élève

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Voici le résultat de la console Hive.

![Requête SerDe 1][image-hdi-hivejson-serde_query1]

Pour calculer la somme des scores du document JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

La requête ci-dessus utilise la fonction définie par l’utilisateur [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) pour développer le tableau des scores afin qu’ils puissent être additionnés.

Voici la sortie de la console Hive.

![Requête SerDe 2][image-hdi-hivejson-serde_query2]

Pour trouver les sujets sur lesquels un étudiant donné a obtenu plus de 80 points :

    SELECT  
      jt.StudentClassCollection.ClassId
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;

Contrairement à get\_json\_object qui retourne une chaîne, la requête ci-dessus retourne un tableau Hive.

![Requête SerDe 3][image-hdi-hivejson-serde_query3]

Si vous souhaitez ignorer le code JSON mal formé, comme l’explique la [page wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de ce SerDe, vous pouvez taper le code suivant :  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




## Résumé
<a id="summary" class="xliff"></a>
En conclusion, le type d'opérateur JSON que vous choisissez dans Hive dépend de votre scénario. Si vous possédez un document JSON simple et que votre recherche ne porte que sur un seul champ, vous pouvez utiliser la fonction UDF Hive get\_json\_object. Si la recherche porte sur plusieurs clés, vous pouvez utiliser json_tuple. Enfin, si vous disposez d'un document imbriqué, il est recommandé d'utiliser le SerDe JSON.

## Étapes suivantes
<a id="next-steps" class="xliff"></a>

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

