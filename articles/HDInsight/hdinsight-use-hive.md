<properties
	pageTitle="Découvrez Hive et comment utiliser HiveQL | Microsoft Azure"
	description="Découvrez Apache Hive et comment l'utiliser avec Hadoop dans HDInsight. Choisissez la façon dont vous souhaitez exécuter votre tâche Hive et utilisez HiveQL pour analyser un exemple de fichier Apache log4j." 
	keywords="hiveql,what is hive"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="04/23/2015"
	ms.author="larryfr"/>

# Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Dans ce didacticiel, apprenez à utiliser Apache Hive dans Hadoop sur HDInsight et choisissez comment exécuter votre tâche Hive. Découvrez également HIveQL et comment analyser un exemple de fichier Apache log4j.

##<a id="why"></a>Qu’est-ce Hive et pourquoi l’utiliser ? 
[Apache Hive](http://hive.apache.org/) est un système d'entrepôt de données pour Hadoop qui permet de résumer des données, d’envoyer des requêtes et d'analyser des données à l’aide de HiveQL (un langage de requête similaire à SQL). Hive peut être utilisé pour explorer vos données de manière interactive ou pour créer des tâches de traitement par lots réutilisables.

Hive vous permet de concevoir une structure sur des données largement non structurées. Une fois que vous avez défini la structure, vous pouvez utiliser Hive pour interroger ces données sans connaître Java ou MapReduce. **HiveQL** (le langage de requête Hive) vous permet d'écrire des requêtes avec des instructions semblables à T-SQL.

Hive sait comment travailler avec des données structurées et semi-structurées, telles que les fichiers texte dans lesquels les champs sont délimités par des caractères spécifiques. Hive prend également en charge un **sérialiseur/ désérialiseur (SerDe)** personnalisé pour des données structurées de manière irrégulière ou complexes. Pour plus d'informations, consultez [Utilisation d’un SerDe JSON personnalisé avec HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

Hive peut également être étendu via des **fonctions définies par l'utilisateur (UDF)**. Une fonction UDF vous permet d'implémenter une fonctionnalité ou une logique qui n'est pas facilement modelée en HiveQL. Pour obtenir un exemple d'utilisation des fonctions définies par l’utilisateur (UDF) avec Hive, consultez les documents suivants :

* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)

* [Utilisation de C# avec Hive et Pig dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Comment ajouter une UDF personnalisée Hive à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="data"></a>Sur les exemples de données, un fichier Apache log4j

Cet exemple utilise un exemple de fichier *log4j*, qui est stocké dans **/example/data/sample.log**, dans votre conteneur de stockage d'objets blob. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité, par exemple :

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l'exemple précédent, le niveau de consignation est ERROR.

> [AZURE.NOTE]Vous pouvez également générer un fichier log4j à l'aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j), puis télécharger ce fichier vers le conteneur d’objet blob. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight](hdinsight-upload-data.md). Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

L’exemple de données est stocké dans le stockage d'objets blob Azure, que HDInsight utilise comme système de fichiers par défaut. HDInsight peut accéder aux fichiers stockés dans des objets blob à l'aide du préfixe **wasb**. Par exemple, pour accéder au fichier sample.log, vous devez utiliser la syntaxe suivante :

	wasb:///example/data/sample.log

Étant donné que le stockage d’objets blob Azure est le stockage par défaut pour HDInsight, vous pouvez également accéder au fichier en utilisant **/example/data/sample.log** depuis HiveQL.

> [AZURE.NOTE]La syntaxe **wasb:///** permet d'accéder à des fichiers stockés dans le conteneur de stockage par défaut de votre cluster HDInsight. Si vous avez indiqué d'autres comptes de stockage lors de l'approvisionnement de votre cluster et que vous souhaitez accéder aux fichiers qui y sont stockés, vous pouvez accéder aux données en indiquant le nom du conteneur et l'adresse du compte de stockage, par exemple : **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Exemple de tâche : projection de colonnes sur des données délimitées

Les instructions HiveQL suivantes vont projeter des colonnes sur des données délimitées stockées dans le **wasb:///example/data** répertoire :

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

Dans l’exemple précédent, les instructions HiveQL effectuent les opérations suivantes :

* **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
* **CREATE EXTERNAL TABLE** : crée une nouvelle table **externe** dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à leur emplacement d’origine et dans leur format d’origine.
* **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
* **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte. Les données peuvent être dans un seul fichier ou réparties sur plusieurs fichiers dans le répertoire.
* **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande renvoie la valeur **3**, car trois lignes contiennent cette valeur.

> [AZURE.NOTE]Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, telle qu’un processus de téléchargement de données automatisé, ou par une autre opération MapReduce et vous souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
>
> La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

Après avoir créé la table externe, les instructions suivantes permettent de créer une table **interne**.

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Ces instructions effectuent les opérations suivantes :

* **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive.
* **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
* **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contient **[ERROR]**, puis insère les données dans la table **errorLogs**.

> [AZURE.NOTE]Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

##<a id="usetez"></a>Utilisation d’Apache Tez pour des performances améliorées

[Apache Tez](http://tez.apache.org) est une infrastructure qui permet une exécution à l'échelle beaucoup plus efficace pour les applications, telles que Hive, qui manipulent de grandes quantités de données. Dans la dernière version de HDInsight, Hive prend en charge l'exécution sur Tez. Cette fonctionnalité est actuellement désactivée par défaut et doit être activée. Pour pouvoir tirer parti de Tez, vous devez définir la valeur suivante pour une requête Hive :

	set hive.execution.engine=tez;

Vous pouvez le faire individuellement pour chaque requête en plaçant cette instruction au début de la requête. Il est également possible d'activer cette fonctionnalité par défaut sur un cluster en définissant la valeur de configuration à la création du cluster. Pour plus d'informations, consultez la page [Approvisionnement des clusters HDInsight](hdinsight-provision-clusters.md).

Les [documents de conception Hive sur Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiennent des informations détaillées sur les options d'implémentation et les configurations du réglage.


##<a id="run"></a>Choisir comment exécuter la tâche HiveQL

HDInsight peut exécuter des tâches HiveQL à l’aide de différentes méthodes. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ... avec ce **système d'exploitation cluster** | ... depuis ce **système d’exploitation client** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux ou Windows | Linux, Unix, Mac OS X ou Windows |
| [Console de requête](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | Basé sur le navigateur |
| [Outils HDInsight pour Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux ou Windows | Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux ou Windows | Windows (pour l’instant) |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux ou Windows | Windows |
| [Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>Étapes suivantes

Maintenant que vous connaissez Hive et que vous avez vu comment l’utiliser avec Hadoop dans HDInsight, utilisez les liens suivants pour découvrir d'autres façons d'utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation des tâches MapReduce avec HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[Powershell-install-configure]: ../install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=54--> 