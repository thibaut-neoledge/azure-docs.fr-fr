<properties
   pageTitle="Analyser et traiter des documents JSON avec Hive dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser des documents JSON et les analyser avec Hive dans HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Comment traiter et analyser des documents JSON dans Hive

JSON est l'un des formats les plus couramment utilisés sur le web. Ce didacticiel aborde l'une des principales questions auxquelles sont confrontés les utilisateurs dans Hive, à savoir comment utiliser et analyser des documents JSON dans Hive.

##Exemple JSON

Prenons un exemple. Supposons que nous disposions du document JSON ci-dessous. Notre objectif est ici d'analyser ce document JSON, puis d'exécuter des requêtes sur ce document (recherche d'une valeur sur une clé, agrégation, etc.).

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

##Aplatissement d'un document JSON (étape uniquement requise si vous utilisez Pretty JSON)

Avant d'utiliser un opérateur Hive quelconque pour effectuer une analyse, nous devons prétraiter le document JSON afin qu'il soit exploitable par Hive.

Toutes les options répertoriées dans la section suivante partent du principe que le document JSON n'occupe qu'une seule ligne. En d'autres termes, nous devons aplatir le document JSON en une chaîne pour qu'il puisse être utilisé par les opérateurs Hive.

**Si votre document JSON est déjà aplati et que l'ensemble du document tient sur une ligne, vous pouvez ignorer cette étape et passer directement à la section suivante sur l'analyse des données JSON.**

Supposons que vous disposiez d'un document JSON non aplati réparti sur plusieurs lignes. Celui-ci se trouve dans votre magasin d'objets blob Azure, dans le dossier */json/input/* sous le conteneur par défaut. Le code suivant crée une table jsonexample qui pointe vers le document JSON non aplati d'origine.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

Créons ensuite une table appelée one_line_json qui pointe vers le document JSON aplati et qui est stockée dans le magasin d'objets blob Azure dans le dossier */json/temp/* sous le conteneur par défaut.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

Pour terminer, remplissons cette table à l'aide des données JSON aplaties.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

À présent, si vous examinez la nouvelle table one_line_json, vous pouvez constater que le document JSON tient sur une seule ligne.

    select * from one_line_json;

Voici la sortie de cette requête :

![Aplatissage du document JSON][image-hdi-hivejson-flatten]

##Options d'analyse de documents JSON dans Hive

Le document JSON étant à présent contenu dans une table à une seule colonne, nous pouvons utiliser Hive pour exécuter des requêtes sur ces données. Hive propose trois mécanismes différents pour exécuter des requêtes sur des documents JSON :

1.	Utilisation de la fonction définie par l'utilisateur (UDF) get_json_object
2.	Utilisation de la fonction UDF json_tuple
3.	Utilisation d'un SerDe personnalisé

Nous allons examiner chaque mécanisme en détail.

##Fonction UDF get_json_object
Hive intègre une fonction UDF appelée [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) qui permet d'exécuter des requêtes sur un document JSON pendant l'exécution. Cette méthode accepte deux arguments : d'une part, le nom de la table et le nom de la méthode contenant le document JSON aplati et, d'autre part, le champ JSON à analyser. Prenons un exemple pour examiner de plus près cette fonction UDF.

Obtenir le prénom et le nom de chaque élève

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

Voici la sortie de cette requête dans la fenêtre de console.

![Fonction UDF get_json_object][image-hdi-hivejson-getjsonobject]

Cette fonction UDF présente quelques limitations.


- L'une des principales limitations a trait aux faibles performances de cette fonction. En effet, chaque champ de la requête nécessite l'analyse de la requête, ce qui affecte les performances.
- Deuxièmement, get_json_object() retourne une représentation sous forme de chaîne d'un tableau. Donc, pour convertir ceci en tableau Hive, vous devez utiliser des expressions régulières pour remplacer les crochets « [ » et « ] », puis appeler split pour obtenir le tableau.


C'est pourquoi le wiki Hive recommande l'utilisation de json_tuple que nous allons aborder ensuite.


##Fonction UDF json_tuple

L'autre fonction UDF fournie par Hive, intitulée [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), est plus performante que [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Cette méthode, qui accepte un ensemble de clés et une chaîne JSON, retourne un tuple de valeurs en utilisant une seule fonction. Prenons un exemple pour examiner de plus près cette fonction UDF.

Obtenir l'identifiant et la classe d'un élève à partir du document JSON

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

Utilisons la syntaxe [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) dans Hive, ce qui permet à json_tuple de créer une table virtuelle en appliquant la fonction UDT à chaque ligne de la table d'origine.

Examinons la sortie de ce script dans la console Hive :

![Fonction UDF json_tuple][image-hdi-hivejson-jsontuple]

Du fait de l'utilisation répétée de LATERAL VIEW, les documents JSON complexes deviennent de plus en plus lourds à traiter Il s'agit là d'un des principaux inconvénients de cette fonction UD . En fait, cette fonction UDF ne peut pas gérer les documents JSON imbriqués.

##Utilisation d'un SerDe personnalisé

Pour analyser des documents JSON imbriqués, SerDe constitue la **meilleure solution**. En effet, comme vous pouvez définir le schéma JSON, vous pouvez très facilement exécuter des requêtes.

Nous allons examiner comment utiliser le SerDe développé par [rcongiu](https://github.com/rcongiu), qui est l'un des plus populaires.

Étape 1 : vérifiez que le [Kit de développement Java SE version 1.7.0_55 (JDK 7u55)](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) est installé. (Remarque : le JDK 1.8 ne fonctionne pas avec ce SerDe.)


- Si vous envisagez d'utiliser le déploiement Windows de HDInsight, choisissez la version Windows x64 du JDK.
- Une fois l'installation terminée, accédez à Panneau de configuration --> Ajouter des variables d'environnement, puis ajoutez une nouvelle variable d'environnement JAVA_HOME pointant vers C:\Program Files\Java\jdk1.7.0_55 ou vers l'emplacement d'installation de votre JDK. Les captures d'écran suivantes montrent comment définir la variable d'environnement.

![Définition de valeurs de configuration correctes pour JDK][image-hdi-hivejson-jdk]

Étape 2: cliquez sur [ce lien](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) pour télécharger Maven 3.3.1. Décompressez l'archive à l'emplacement dans lequel vous souhaitez stocker les fichiers binaires. Dans mon cas, je vais la décompresser dans C:\Program Files\Maven. Ajoutez le dossier bin à votre chemin d'accès. Pour cela, accédez à Panneau de configuration --> Modifier les variables d'environnement système --> Variables d'environnement. La capture d'écran ci-dessous montre comment effectuer cette opération.

![Configuration de Maven][image-hdi-hivejson-maven]

Étape 3: clonez le projet à partir du site github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Pour cela, cliquez sur le bouton « Download Zip » illustré dans la capture d'écran ci-dessous.

![Clonage du projet][image-hdi-hivejson-serde]

Étape 4 : accédez au dossier dans lequel vous avez téléchargé ce package et tapez « mvn package ». Cette action doit créer les fichiers jar nécessaires que vous pouvez ensuite copier dans le cluster.

Étape 5 : accédez ensuite au dossier cible sous le dossier racine dans lequel vous avez téléchargé le package. Téléchargez le fichier json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar dans le nœud principal de votre cluster. En général, je le mets dans le dossier binaire Hive (C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin) ou un dossier semblable.
 
Étape 6 : à l'invite Hive, tapez « add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.ja ». Comme dans le cas présent, le fichier jar se trouve dans le dossier C:\apps\dist\hive-0.13.x\bin, je peux directement l'ajouter avec le nom comme indiqué ci-dessous :

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![Ajout de JAR à votre projet][image-hdi-hivejson-addjar]

Nous pouvons dorénavant utiliser le SerDe pour exécuter des requêtes sur le document JSON.

Avant toute chose, créons la table avec le schéma du document JSON. Notez qu'il est possible de gérer beaucoup des types beaucoup plus riches, notamment des mappages, des tableaux et des structures. Tapez le code ci-dessous dans la console Hive pour créer une table nommée json_table qui peut lire le document JSON basé sur le schéma ci-dessous.

    drop table json_table;
    create external table json_table (
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
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

Une fois le schéma défini, exécutons quelques exemples pour voir comment exécuter des requêtes sur le document JSON avec Hive :

a) Répertorier le prénom et le nom de l'élève

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Voici le résultat de la console Hive.

![Requête SerDe 1][image-hdi-hivejson-serde_query1]

b) Cette requête calcule la somme des scores du document JSON

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
La requête ci-dessus utilise la fonction UDF [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) pour développer le tableau des scores afin de les additionner.

Voici la sortie de la console Hive.

![Requête SerDe 2][image-hdi-hivejson-serde_query2]

c) Rechercher les matières dans lesquelles l'élève à obtenu un score de plus de 80 points : select jt.StudentClassCollection.ClassId from json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
Contrairement à get_json_object qui retourne une chaîne, la requête ci-dessus retourne un tableau Hive.

![Requête SerDe 3][image-hdi-hivejson-serde_query3]

Pour ignorer les données JSON mal formées, vous pouvez, comme l'explique la [page wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de ce SerDe, taper le code suivant :

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##Autres options
Outre les options répertoriées ci-dessous, vous pouvez également écrire votre propre code personnalisé à l'aide de Python ou d'autres langages spécifiques à votre scénario. Quand votre script Python est prêt, consultez [cet article][hdinsight-python] qui traite de l'exécution de votre propre code Python avec Hive.

##Résumé
En conclusion, le type d'opérateur JSON que vous choisissez dans Hive dépend de votre scénario. Si vous possédez un document JSON simple et que votre recherche ne porte que sur un seul champ, vous pouvez utiliser la fonction UDF Hive get_json_object. Si votre recherche porte sur plusieurs clés, vous pouvez utiliser json_tuple. Enfin, si vous disposez d'un document imbriqué, il est recommandé d'utiliser le SerDe JSON.

L'objectif de HDInsight est de vous permettre d'utiliser davantage de formats en mode natif dans Hive sans nécessiter d'efforts considérables de votre part. Dès que nous aurons d'autres informations à partager, nous mettrons à jour ce didacticiel.

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
 

<!---HONumber=62-->