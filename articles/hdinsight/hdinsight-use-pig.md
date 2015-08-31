<properties
   pageTitle="Utilisation de Hadoop Pig dans HDInsight | Microsoft Azure"
   description="Utilisation de Pig avec Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# Utilisation de Pig avec Hadoop sur HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) est une plateforme qui permet de créer des programmes pour Hadoop dans un langage procédural appelé *Pig Latin*. Pig est une alternative à Java, dédiée à la création de solutions *MapReduce*, qui est incluse avec Azure HDInsight.

Dans cet article, vous découvrirez comment utiliser Pig avec HDInsight.

##<a id="why"></a>Pourquoi utiliser Pig ?

Lun des défis de traitement de données à l'aide de MapReduce dans Hadoop consiste à implémenter votre logique de traitement en utilisant uniquement un mappage et une fonction de réduction. Pour un traitement complexe, vous devez souvent interrompre le traitement lors de plusieurs opérations MapReduce qui sont chaînées ensemble pour produire le résultat souhaité.

Au lieu de vous obliger à utiliser uniquement le mappage et des fonctions de réduction, Pig permet de définir un traitement comme une série de transformations via lesquelles les données circulent pour produire le résultat souhaité.

Le langage Pig Latin vous permet de décrire le flux de données provenant d’une entrée brute, via une ou plusieurs transformations, pour produire le résultat souhaité. Les programmes Pig Latin suivent le modèle général suivant :

- **Chargement** : lecture des données à manipuler dans le système de fichiers
- **Transformation** : manipulation des données
- **Sortie ou stockage** : affichage du résultat à l'écran ou stockage pour traitement

Pig Latin prend également en charge les fonctions définies par l'utilisateur (UDF), ce qui vous permet d'appeler des composants externes qui implémentent la logique qui est difficile à modéliser dans Pig Latin.

Pour plus d'informations sur Pig Latin, consultez les pages [Manuel de référence Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) et [Manuel de référence Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Pour obtenir un exemple d'utilisation des fonctions définies par l’utilisateur (UDF) avec Pig, consultez les documents suivants :

* [Utilisation de Python avec Pig et Hive dans HDInsight](hdinsight-python.md)

* [Utilisation de C# avec Hive et Pig dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>À propos de l'exemple de données

Cet exemple utilise un exemple de fichier *log4j*, qui est stocké dans **/example/data/sample.log**, dans votre conteneur de stockage d'objets blob. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité, par exemple :

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l'exemple précédent, le niveau de journal est ERROR.

> [AZURE.NOTE]Vous pouvez également générer un fichier log4j à l'aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j), puis télécharger ce fichier vers votre objet blob. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight](hdinsight-upload-data.md). Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

L’exemple de données est stocké dans le module de stockage d'objets blob Azure, que HDInsight utilise comme système de fichiers par défaut pour les clusters Hadoop. HDInsight peut accéder aux fichiers stockés dans des objets blob à l'aide du préfixe **wasb**. Par exemple, pour accéder au fichier sample.log, vous devez utiliser la syntaxe suivante :

	wasb:///example/data/sample.log

Étant donné que WASB est le stockage par défaut pour HDInsight, vous pouvez également accéder au fichier en utilisant **/example/data/sample.log** depuis Pig Latin.

> [AZURE.NOTE]La syntaxe, ****wasb:///**, permet d'accéder à des fichiers stockés dans le conteneur de stockage par défaut de votre cluster HDInsight. Si vous avez indiqué d'autres comptes de stockage lors de l'approvisionnement du cluster et que vous souhaitez accéder aux fichiers qui y sont stockés, vous pouvez accéder aux données en indiquant le nom du conteneur et l'adresse du compte de stockage, par exemple : ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>À propos de l'exemple de tâche

La tâche Pig Latin suivante charge le fichier **sample.log** depuis le stockage par défaut de votre cluster HDInsight. Elle effectue ensuite une série de transformations qui créent un décompte du nombre de fois où chaque niveau du journal s'est produit dans les données d'entrée. Les résultats sont vidés dans STDOUT.

	LOGS = LOAD 'wasb:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

L’image suivante montre une répartition de ce qu’effectue chaque transformation sur les données.

![Représentation graphique des transformations][image-hdi-pig-data-transformation]

##<a id="run"></a>Exécution de la tâche Pig Latin

HDInsight peut exécuter des tâches Pig Latin de différentes façons. Utilisez le tableau suivant pour découvrir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ... avec ce **système d'exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | &nbsp; | ✔ | Linux ou Windows | Linux, Unix, Mac OS X ou Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux ou Windows | Windows (pour l’instant) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | &nbsp; | ✔ | Linux ou Windows | Windows |
| [Bureau à distance](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>Étapes suivantes

Maintenant que vous avez vu comment utiliser Pig avec HDInsight, utilisez les liens suivants pour découvrir d'autres façons d'utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation des tâches MapReduce avec HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=August15_HO8-->