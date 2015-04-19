<properties
   pageTitle="Utilisation de Hadoop Hive dans HDInsight | Azure"
   description="Découvrez comment utiliser Hive avec la console de requêtes Web HDInsight."
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

# Exécution de requêtes Hive à l'aide de la console de requêtes

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous découvrirez comment utiliser la console de requêtes HDInsight pour exécuter des requêtes Hive sur un cluster Hadoop HDInsight à partir de votre navigateur.

> [AZURE.NOTE] La console de requêtes n'est disponible que sur les clusters HDInsight Windows

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop HDInsight Windows

* Un navigateur Web moderne

##<a id="run"></a> Exécution de requêtes Hive à l'aide de la console de requêtes

1. Ouvrez le <a href="https://manage.windowsazure.com" target="_blank">portail de gestion Azure</a> et sélectionnez votre cluster HDInsight. En bas de la page, sélectionnez **Console de requêtes**. À l'invite, saisissez le nom d'utilisateur et le mot de passe que vous avez utilisés lors de la création du cluster.

    > [AZURE.NOTE] Vous pouvez également accéder à la console de requêtes en entrant **https://CLUSTERNAME.azurehdinsight.net** dans votre navigateur.

2. À partir des liens situés en haut de la page, sélectionnez **Éditeur Hive**. Ceci affiche un formulaire qui peut être utilisé pour saisir des instructions HiveQL que vous souhaitez exécuter sur le cluster HDInsight. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	Remplacez le texte  `Select * from hivesampletable` par les instructions HiveSQL suivantes :

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
    * **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte
    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit renvoyer une valeur de **3**, car trois lignes contiennent cette valeur.

2. Sélectionnez **Envoyer**. La **session de la tâche** située au bas de la page devrait afficher les détails de la tâche.

3. Une fois les champs **États** définis sur **Terminé**, sélectionnez **Afficher les détails** de la tâche. Dans la page relative aux détails, le **résultat de la tâche** contiendra [ERROR]	3. Vous pouvez utiliser le bouton **Télécharger**, situé en dessous de ce champ, pour télécharger un fichier contenant le résultat de la tâche.


##<a id="summary"></a>Résumé

Comme vous pouvez le constater, la console de requêtes permet d'exécuter facilement des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat. 

Pour en savoir plus sur Hive à l'aide de la console de requêtes, sélectionnez **Prise en main** en haut de la console de requêtes, puis utilisez les exemples. Chaque exemple aborde le processus d'analyse de données à l'aide de Hive, y compris les explications des instructions HiveQL utilisées dans l'exemple.

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
