---
title: "Découvrez Hive et comment utiliser HiveQL | Microsoft Docs"
description: "Découvrez Apache Hive et comment l&quot;utiliser avec Hadoop dans HDInsight. Choisissez comment exécuter votre tâche Hive et utilisez HiveQL pour analyser un exemple de fichier Apache log4j."
keywords: "hiveql, présentation de hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: db324cc8269a1f23613e5193f5a1dec1d26a62b1
ms.lasthandoff: 04/17/2017


---
# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight"></a>Utiliser Hive et HiveQL avec Hadoop dans HDInsight

Découvrez comment utiliser Hive avec HDInsight. Utilisez la table suivante pour découvrir les différentes façons d’utiliser Hive avec HDInsight :

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:---:|:---:|:--- |:--- |
| [Affichage Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |N’importe lequel (basé sur le navigateur) |
| [Commande Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Console de requête](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 et 3.3) |&nbsp; |✔ |Windows |N’importe lequel (basé sur le navigateur) |
| [Outils HDInsight pour Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 et 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence d’HDInsight 3.2 et 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a id="why"></a>Présentation de Hive

[Apache Hive](http://hive.apache.org/) est un système d’entrepôt de données pour Hadoop qui permet de résumer des données, d’envoyer des requêtes et d’analyser des données. Hive vous permet de concevoir une structure sur des données largement non structurées. Une fois que vous avez défini la structure, vous pouvez utiliser Hive pour interroger ces données sans connaître Java ou MapReduce.

Les requêtes Hive sont écrites dans le langage HiveQL, qui est un langage de requête semblable à SQL. Hive peut être utilisé pour explorer vos données de manière interactive ou pour créer des tâches de traitement par lots réutilisables.

Hive sait comment travailler avec des données structurées et semi-structurées, telles que les fichiers texte dans lesquels les champs sont délimités par des caractères spécifiques. Hive prend également en charge un **sérialiseur/ désérialiseur (SerDe)** personnalisé pour des données structurées de manière irrégulière ou complexes. Pour plus d’informations, consultez le document [Utilisation d’un SerDe JSON personnalisé avec HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Fonctions définies par l’utilisateur (UDF)

Hive peut également être étendu via des **fonctions définies par l'utilisateur (UDF)**. Une fonction UDF vous permet d'implémenter une fonctionnalité ou une logique qui n'est pas facilement modelée en HiveQL. Pour obtenir un exemple d’utilisation des fonctions définies par l’utilisateur (UDF) avec Hive, consultez les documents suivants :

* [Utiliser une fonction définie par l’utilisateur de Java avec Hive](hdinsight-hadoop-hive-java-udf.md)

* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)

* [Utilisation de C# avec Hive et Pig dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Comment ajouter une UDF personnalisée Hive à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Exemple d’UDF Hive personnalisée pour convertir les formats date/heure en horodatage Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Tables internes et externes Hive

Voici quelques éléments à connaître sur les tables interne et externe Hive :

* La commande **CREATE TABLE** permet de créer une table interne. Le fichier de données doit se trouver dans le conteneur par défaut.

* La commande **CREATE TABLE** déplace le fichier de données vers le répertoire `/hive/warehouse/<TableName>`, sur le stockage par défaut pour le cluster.

* La commande **CREATE EXTERNAL TABLE** permet de créer une table externe. Le fichier de données peut se trouver à l'extérieur du conteneur par défaut.

* La commande **CREATE EXTERNAL TABLE** ne déplace pas le fichier de données.

Pour plus d’informations, consultez la rubrique [HDInsight : introduction aux tables interne et externe Hive][cindygross-hive-tables].

## <a id="data"></a>Exemple de données

HDInsight propose différents exemples de jeux de données, qui sont stockés dans les répertoires `/example/data` et `/HdiSamples`. Ces répertoires sont disponibles dans le stockage par défaut de votre cluster. Ce document utilise le fichier `/example/data/sample.log`. Il s’agit d’un fichier *log4j*. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l'exemple précédent, le niveau de consignation est ERROR.

> [!NOTE]
> Vous pouvez également générer un fichier log4j à l'aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) , puis télécharger ce fichier vers le conteneur d’objet blob. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight](hdinsight-upload-data.md) . Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Exemple de requête Hive

Les instructions HiveQL suivantes projettent des colonnes sur le fichier `/example/data/sample.log` :

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Dans l’exemple précédent, les instructions HiveQL effectuent les opérations suivantes :

* **set hive.execution.engine=tez;** : définit le moteur d’exécution pour utiliser Tez. L’utilisation de Tez au lieu de MapReduce peut augmenter les performances de requête. Pour plus d’informations sur Tez, consultez la section [Utilisation d’Apache Tez pour des performances améliorées](#usetez) .

    > [!NOTE]
    > Cette instruction est uniquement requise lorsque vous utilisez un cluster HDInsight sous Windows. Tez est le moteur d’exécution par défaut pour HDInsight sous Linux.

* **DROP TABLE** : si la table existe déjà, supprimez-la.

* **CREATE EXTERNAL TABLE** : crée une nouvelle table **externe** dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l’emplacement d’origine, dans le format d’origine.

* **ROW FORMAT**: indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.

* **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire `example/data`) et qu’elles sont stockées sous forme de texte. Les données peuvent être dans un seul fichier ou réparties sur plusieurs fichiers dans le répertoire.

* **SELECT** : sélectionne toutes les lignes où la colonne **t4** contient la valeur **[ERROR]**. Cette instruction renvoie la valeur **3**, car trois lignes contiennent cette valeur.

* **INPUT__FILE__NAME LIKE '%.log'** : indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log. Cette instruction limite la recherche au fichier `sample.log` qui contient les données.

> [!NOTE]
> Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, Par exemple, un processus de chargement de données automatisé ou une opération MapReduce.
>
> La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

Pour créer une table **interne** plutôt qu’externe, utilisez le code HiveQL suivant :

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Ces instructions effectuent les opérations suivantes :

* **CREATE TABLE IF NOT EXISTS**: si la table n’existe pas, créez-la. Le mot-clé **EXTERNAL** n’étant pas utilisé, cette instruction crée une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive.

* **STORED AS ORC**: stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.

* **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contient **[ERROR]**, puis insère les données dans la table **errorLogs**.

> [!NOTE]
> Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

## <a id="usetez"></a>Utilisation d’Apache Tez pour des performances améliorées

[Apache Tez](http://tez.apache.org) est une infrastructure qui permet une exécution à l'échelle beaucoup plus efficace pour les applications, telles que Hive, qui manipulent de grandes quantités de données. Dans la dernière version de HDInsight, Hive prend en charge l'exécution sur Tez. Tez est activé par défaut pour les clusters HDInsight basés sur Linux.

> [!NOTE]
> Tez est actuellement désactivé par défaut pour les clusters HDInsight basés sur Windows, et il doit être activé. Pour pouvoir tirer parti de Tez, vous devez définir la valeur suivante pour une requête Hive :
>
> `set hive.execution.engine=tez;`
>
> Tez est le moteur par défaut pour les clusters HDInsight sous Linux.

Les [documents de conception Hive sur Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiennent des informations détaillées sur les options d’implémentation et les configurations du réglage.

Pour faciliter le débogage des tâches exécutées en utilisant Tez, HDInsight fournit les interfaces utilisateur web suivantes qui vous permettent d'afficher les détails des tâches Tez :

* [Utilisez la vue Tez Ambari sur HDInsight Linux](hdinsight-debug-ambari-tez-view.md)

* [Utiliser l'interface utilisateur Tez sur HDInsight Windows](hdinsight-debug-tez-ui.md)

## <a id="run"></a>Exécuter la tâche HiveQL

HDInsight peut exécuter des tâches HiveQL de différentes manières. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:---:|:---:|:--- |:--- |
| [Affichage Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |N’importe lequel (basé sur le navigateur) |
| [Commande Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Console de requête](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 et 3.3) |&nbsp; |✔ |Windows |N’importe lequel (basé sur le navigateur) |
| [Outils HDInsight pour Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 et 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence d’HDInsight 3.2 et 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Tâches Hive et SQL Server Integration Services

Vous pouvez également utiliser les services SQL Server Integration Services (SSIS) pour exécuter une tâche Hive. Le pack de fonctionnalités Azure pour SSIS fournit les composants suivants, compatibles avec les tâches Hive sur HDInsight.

* [Tâche Hive d’Azure HDInsight][hivetask]

* [Gestionnaire de connexions d’abonnement Azure][connectionmanager]

Pour en savoir plus sur le pack de fonctionnalités Azure pour SSIS, cliquez [ici][ssispack].

## <a id="nextsteps"></a>Étapes suivantes

Maintenant que vous connaissez Hive et que vous avez vu comment l’utiliser avec Hadoop dans HDInsight, utilisez les liens suivants pour découvrir d'autres façons d'utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation des tâches MapReduce avec HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

