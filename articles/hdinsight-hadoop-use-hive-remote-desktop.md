<properties
   pageTitle="Utilisation de Hadoop Hive dans HDInsight | Azure"
   description="Découvrez comment utiliser Hive avec HDInsight via le Bureau à distance."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Utilisation de Hive avec Hadoop sur HDInsight via le Bureau à distance

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous découvrirez comment vous connecter à un cluster HDInsight à l'aide du Bureau à distance, et exécuter ensuite des requêtes Hive à l'aide de l'interface de ligne de commande (CLI) Hive.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d'informations sur le code HiveQL utilisé dans cet exemple, consultez <a href="../hdinsight-use-hive/" target="_blank">Utilisation de Hive avec Hadoop sur HDInsight</a>.

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster HDInsight Windows (Hadoop sur HDInsight)

* Windows 7 ou un système d'exploitation client plus récent

##<a id="connect"></a>Connectez-vous avec le Bureau à distance

Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Se connecter à des clusters HDInsight avec RDP</a>.

##<a id="hive"></a>Utiliser la commande Hive

Une fois connecté au Bureau pour le cluster HDInsight, effectuez les étapes suivantes pour utiliser Hive.

1. Depuis le Bureau HDInsight, démarrez la **Ligne de commande Hadoop**.

2. Exécutez la commande suivante pour démarrer l'interface de ligne de commande (CLI) Hive.

        %hive_home%\bin\hive

    Une fois l'interface de ligne de commande lancée, vous verrez apparaître l'invite CLI : `hive>`.

3. En utilisant l'interface de ligne de commande, saisissez les instructions suivantes pour créer une table nommée **log4jLogs** à l'aide de l'exemple de données :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
    
    * **CREATE EXTERNAL TABLE** : crée une table  'externe' dans Hive. Les tables externes stockent uniquement la définition de table dans Hive ; les données restent à leur emplacement d'origine

		> [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
    	>
    	> La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.
    
	* **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
	
    * **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte.
    
    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit renvoyer une valeur de **3**, car trois lignes contiennent cette valeur.


4. Utilisez les instructions suivantes pour créer une nouvelle table  'interne' nommée **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Ces instructions effectuent les opérations suivantes :

    * **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé **EXTERNAL** n'étant pas utilisé, il s'agit d'une table interne, stockée dans l'entrepôt de données Hive et gérée intégralement par Hive.
    
		> [AZURE.NOTE] Contrairement aux tables **EXTERNAL**, la suppression d'une table interne entraîne également la suppression des données sous-jacentes.
		
    * **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive
    
    * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.

    Pour vérifier que seules les lignes contenant **[ERROR]** dans la colonne t4 ont été stockées dans la table **errorLogs**, utilisez l'instruction suivante afin de renvoyer toutes les lignes à partir de **errorLogs**.

        SELECT * from errorLogs;

    Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4.

##<a id="summary"></a>Résumé

Comme vous pouvez le constater, la commande Hive permet d'exécuter facilement, et de façon interactive, des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour découvrir d'autres manières d'utiliser Hadoop sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
