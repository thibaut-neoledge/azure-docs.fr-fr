<properties
   pageTitle="Utilisation de Hadoop Hive dans HDInsight | Azure"
   description="Découvrez comment utiliser Hive avec HDInsight via SSH."
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

#Utilisation de Hive avec Hadoop dans HDInsight via SSH

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous découvrirez comment utiliser Secure Shell (SSH) pour vous connecter à un cluster Hadoop sur Azure HDInsight, et envoyer ensuite des requêtes Hive à l'aide de l'interface de ligne de commande (CLI) Hive.

> [AZURE.NOTE] Si vous vous êtes déjà familiarisé avec l'utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez <a href="../hdinsight-hadoop-linux-information/" target="_blank">Ce qu'il faut savoir sur Hadoop dans HDInsight sous Linux</a>.

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop Linux sur HDInsight.

* Un client SSH. Mac OS, Linux et Unix doivent être accompagnés d'un client SSH. Les utilisateurs Windows doivent télécharger un client, comme <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Connexion avec SSH

Connectez-vous au nom de domaine complet de votre cluster HDInsight à l'aide de la commande SSH. Le nom de domaine complet est le nom attribué au cluster, suivi de **.azurehdinsight.net**. Par exemple, la commande suivante permettrait de se connecter à un cluster nommé **myhdinsight** :

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l'authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez spécifier l'emplacement de la clé privée sur votre système client :

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l'authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

###PuTTy (clients Windows)

Windows ne fournit pas de client SSH intégré. Nous vous recommandons d'utiliser **Putty**, qui peut être téléchargé à partir de <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Pour plus d'informations sur l'utilisation de Putty, consultez la section **Utilisation de Putty pour se connecter à un ordinateur Linux** de la rubrique <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Utilisation de SSH avec Linux sur Azure</a>.

> [AZURE.NOTE] Si vous avez utilisé un certificat pour l'authentification SSH pour le cluster HDInsight, vous devez également consulter la section **Créer une clé privée pour Putty** de la rubrique <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Utilisation de SSH avec Linux sur Azure</a>.

##<a id="hive"></a>Utiliser la commande Hive

2. Une fois connecté, démarrez l'interface de ligne de commande Hive à l'aide de la commande suivante :

        hive

3. À l'aide de l'interface de ligne de commande, entrez les instructions suivantes pour créer une table nommée **log4jLogs** avec les exemples de données suivants :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
    * **CREATE EXTERNAL TABLE** : crée une table  'externe' dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.
    * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
    * **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte.
    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit retourner la valeur **3**, car trois lignes contiennent cette valeur.

    > [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
    >
    > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

4. Utilisez les instructions suivantes pour créer une table  'interne' nommée **errorLogs** :

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Ces instructions effectuent les opérations suivantes :

    * **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé **EXTERNAL** n'étant pas utilisé, il s'agit d'une table interne, stockée dans l'entrepôt de données Hive et gérée intégralement par Hive.
    * **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
    * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.

    Pour vérifier que seules les lignes contenant **[ERROR]** dans la colonne t4 ont été stockées dans la table **errorLogs**, utilisez l'instruction suivante afin de renvoyer toutes les lignes à partir de **errorLogs** :

        SELECT * from errorLogs;

    Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4.

    > [AZURE.NOTE] Contrairement aux tables externes, la suppression d'une table interne entraîne également la suppression des données sous-jacentes.

##<a id="summary"></a>Résumé

Comme vous pouvez le constater, la commande Hive permet d'exécuter facilement et de façon interactive des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](../hdinsight-use-hive/)

Pour plus d'informations sur d'autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](../hdinsight-use-pig/)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](../hdinsight-use-mapreduce/)

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

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
