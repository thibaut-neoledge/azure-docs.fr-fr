---
title: Utilisation de Hadoop Pig dans HDInsight | Microsoft Docs
description: Utilisation de Pig avec Hadoop sur HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 8b924f29594a9f0c8c161661b216a12ff65b42a9
ms.lasthandoff: 01/18/2017


---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utilisation de Pig avec Hadoop sur HDInsight
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) est une plateforme qui permet de créer des programmes pour Hadoop dans un langage procédural appelé *Pig Latin*. Pig est une alternative à Java, dédiée à la création de solutions *MapReduce* , qui est incluse avec Azure HDInsight.

Dans cet article, vous découvrirez comment utiliser Pig avec HDInsight.

## <a name="a-idwhyawhy-use-pig"></a><a id="why"></a>Pourquoi utiliser Pig ?
Lun des défis de traitement de données à l'aide de MapReduce dans Hadoop consiste à implémenter votre logique de traitement en utilisant uniquement un mappage et une fonction de réduction. Pour un traitement complexe, vous devez souvent interrompre le traitement lors de plusieurs opérations MapReduce qui sont chaînées ensemble pour produire le résultat souhaité.

Au lieu de vous obliger à utiliser uniquement le mappage et des fonctions de réduction, Pig permet de définir un traitement comme une série de transformations via lesquelles les données circulent pour produire le résultat souhaité.

Le langage Pig Latin vous permet de décrire le flux de données provenant d’une entrée brute, via une ou plusieurs transformations, pour produire le résultat souhaité. Les programmes Pig Latin suivent le modèle général suivant :

* **Chargement**: lecture des données à manipuler dans le système de fichiers
* **Transformation**: manipulation des données
* **Sortie ou stockage**: affichage du résultat à l'écran ou stockage pour traitement

Pig Latin prend également en charge les fonctions définies par l'utilisateur (UDF), ce qui vous permet d'appeler des composants externes qui implémentent la logique qui est difficile à modéliser dans Pig Latin.

Pour plus d’informations sur Pig Latin, consultez les pages [Manuel de référence Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) et [Manuel de référence Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Pour obtenir un exemple d'utilisation des fonctions définies par l’utilisateur (UDF) avec Pig, consultez les documents suivants :

* [Utilisation de DataFu avec Pig dans HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) : DataFu est une collection de fonctions définies par l’utilisateur utiles gérées par Apache
* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)
* [Utilisation de C# avec Hive et Pig dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a name="a-iddataaabout-the-sample-data"></a><a id="data"></a>À propos de l'exemple de données
Cet exemple utilise un exemple de fichier *log4j* , qui est stocké dans **/example/data/sample.log** , dans votre conteneur de stockage d'objets blob. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l'exemple précédent, le niveau de journal est ERROR.

> [!NOTE]
> Vous pouvez également générer un fichier log4j à l'aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) , puis télécharger ce fichier vers votre objet blob. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight](hdinsight-upload-data.md) . Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).
> 
> 

L’exemple de données est stocké dans le module de stockage d'objets blob Azure, que HDInsight utilise comme système de fichiers par défaut pour les clusters Hadoop. HDInsight peut accéder aux fichiers stockés dans des objets blob à l'aide du préfixe **wasb** . Par exemple, pour accéder au fichier sample.log, vous devez utiliser la syntaxe suivante :

    wasbs:///example/data/sample.log

Étant donné que WASB est le stockage par défaut pour HDInsight, vous pouvez également accéder au fichier en utilisant **/example/data/sample.log** depuis Pig Latin.

> [!NOTE]
> La syntaxe, **wasbs:///**, permet d’accéder à des fichiers stockés dans le conteneur de stockage par défaut de votre cluster HDInsight. Si vous avez indiqué d’autres comptes de stockage pendant l’approvisionnement du cluster et que vous souhaitez accéder aux fichiers qui y sont stockés, vous pouvez accéder aux données en indiquant le nom du conteneur et l’adresse du compte de stockage, par exemple : **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.
> 
> 

## <a name="a-idjobaabout-the-sample-job"></a><a id="job"></a>À propos de l'exemple de tâche
La tâche Pig Latin suivante charge le fichier **sample.log** depuis le stockage par défaut de votre cluster HDInsight. Elle effectue ensuite une série de transformations qui créent un décompte du nombre de fois où chaque niveau du journal s'est produit dans les données d'entrée. Les résultats sont vidés dans STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

L’image suivante montre une répartition de ce qu’effectue chaque transformation sur les données.

![Représentation graphique des transformations][image-hdi-pig-data-transformation]

## <a name="a-idrunarun-the-pig-latin-job"></a><a id="run"></a>Exécution de la tâche Pig Latin
HDInsight peut exécuter des tâches Pig Latin de différentes façons. Utilisez le tableau suivant pour découvrir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](hdinsight-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (pour l’instant) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Bureau à distance](hdinsight-hadoop-use-pig-remote-desktop.md) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Exécute des tâches Pig sur Azure HDInsight à l’aide de services SQL Server Integration Services locaux
Vous pouvez également utiliser les services SQL Server Integration Services (SSIS) pour exécuter une tâche Pig. Le pack de fonctionnalités Azure pour SSIS fournit les composants suivants, compatibles avec les tâches Pig sur HDInsight.

* [Tâche d’Azure HDInsight Pig][pigtask]
* [Gestionnaire de connexions d’abonnement Azure][connectionmanager]

Pour en savoir plus sur le pack de fonctionnalités Azure pour SSIS, cliquez [ici][ssispack].

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Étapes suivantes
Maintenant que vous avez vu comment utiliser Pig avec HDInsight, utilisez les liens suivants pour découvrir d'autres façons d'utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Sqoop avec HDInsight](hdinsight-use-sqoop.md)
* [Utilisation d’Oozie avec HDInsight](hdinsight-use-oozie.md)
* [Utilisation des tâches MapReduce avec HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx


[hdinsight-upload-data]: hdinsight-upload-data.md

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif


