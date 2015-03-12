<properties
   pageTitle="Utilisation de Hadoop Pig dans HDInsight | Azure"
   description="Découvrez comment transmettre à distance des tâches Pig vers HDInsight à l'aide de Curl."
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

#Exécution de requêtes Hive avec Hadoop dans HDInsight via Curl

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Dans ce document, vous découvrirez comment utiliser Curl pour exécuter des requêtes Hive sur un cluster Hadoop sur HDInsight. 

Curl est utilisé pour vous montrer comment vous pouvez interagir avec HDInsight à l'aide de requêtes brutes HTTP pour exécuter, surveiller et récupérer les résultats des requêtes Hive. Cet outil utilise l'API REST WebHCat (anciennement connu sous le nom de Templeton) fournie par votre cluster de HDInsight.

> [AZURE.NOTE] Si vous utilisez déjà sans problème les serveurs Hadoop Linux, mais que vous ignorez totalement comment fonctionne HDInsight, consultez la rubrique <a href="../hdinsight-hadoop-linux-information/" target="_blank">Ce que vous devez savoir concernant Hadoop sur HDInsight Linux</a>.

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight (Linux ou Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

##<a id="curl"></a>Exécution de requêtes Hive à l'aide de Curl

> [AZURE.NOTE] Lors de l'utilisation de Curl ou de toute autre communication REST avec WebHCat, vous devez authentifier vos requêtes en fournissant au cluster HDInsight le nom d'utilisateur et le mot de passe de l'administrateur. Vous devez également utiliser le nom du cluster comme partie intégrante de l'URI utilisé pour envoyer les requêtes vers le serveur.
> 
> Dans les commandes de cette section, remplacez **USERNAME** par votre nom d'utilisateur, pour vous authentifier sur le cluster, et **PASSWORD** par le mot de passe du compte d'utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
> 
> L'API REST est sécurisée via <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">l'authentification de base</a>. Vous devriez toujours soumettre vos requêtes à l'aide de HTTPS pour vous assurer que vos informations d'identification sont bien envoyés au serveur.

1. Depuis une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devez recevoir une réponse ayant l'aspect suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** : le nom d'utilisateur et le mot de passe utilisés pour authentifier la requête
    * **-G** : indique qu'il s'agit d'une requête GET

    Le début de l'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera toujours le même pour toutes les requêtes. Le chemin, **/status**, indique que la requête doit renvoyer l'état de WebHCat (également connu sous le nom de Templeton) sur le serveur. Vous pouvez également prendre connaissance de la version de Hive à l'aide de la commande suivante.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Vous devez renvoyer une réponse ayant l'aspect suivant :

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Utilisez la commande suivante pour créer une nouvelle table nommée **log4jLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : `-G` n'étant pas utilisé, la requête opte par défaut pour la méthode POST. `-d` : spécifie les valeurs de données qui sont envoyées avec la requête

        * **user.name** : l'utilisateur qui exécute la commande
        
        * **execute** : les instructions HiveQL qui doivent être exécutées
        
        * **statusdir** : le répertoire sur lequel l'état de cette tâche sera écrit

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
    
    * **CREATE EXTERNAL TABLE** : crée une nouvelle table 'external' dans Hive. Les tables externes stockent uniquement la définition de table dans Hive ; les données restent à leur emplacement d'origine

		> [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
		>
		> La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

    * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
    
    * **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (répertoire example/data) et précise qu'elles sont stockées sous la forme de texte
    
    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit renvoyer une valeur de **3**, car trois lignes contiennent cette valeur.

    > [AZURE.NOTE] Notez que les espaces entre les instructions HiveQL sont remplacés par le caractère `+` lorsqu'ils sont utilisés avec Curl. Les valeurs citées qui contiennent un espace, tel que le séparateur, ne doivent pas être remplacées par `+`.

    Cette commande doit renvoyer l'identificateur de la tâche qui peut être utilisé pour vérifier l'état de cette tâche.

        {"id":"job_1415651640909_0026"}

3. Pour vérifier l'état de la tâche, utilisez la commande suivante : Remplacez le **JOBID** par la valeur renvoyée dans l'étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le JOBID serait `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Lorsque la tâche est terminée, celle-ci est définie par la valeur " TERMINÉ ".

    > [AZURE.NOTE] La requête Curl renvoie un document JSON comportant des informations sur cette tâche ; jq ne récupère, quant à lui, que la valeur de l'état. 

4. Une fois l'état de la tâche défini sur **TERMINÉ**, vous pouvez récupérer les résultats de la tâche à partir du stockage d'objets blob. Le paramètre `statusdir` transmis avec la requête contient l'emplacement du fichier de sortie ; ici **wasb:///example/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **example/curl**, sur le conteneur de stockage utilisé par défaut par votre cluster HDInsight.

    Vous pouvez répertorier et télécharger ces fichiers à l'aide de <a href="../xplat-cli/" target="_blank">l'interface de ligne de commande interplateforme Azure (xplat-cli)</a>. Par exemple, pour répertorier les fichiers dans **example/curl**, utilisez la commande suivante :

		azure storage blob list <container-name> example/curl

	Pour télécharger un fichier, utilisez le code suivant :

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Vous devez soit spécifier le nom du compte de stockage qui contient l'objet blob utilisant les paramètres `-a` et `-k`, soit définir les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**. Consultez la rubrique <a href="../hdinsight-upload-data/" target="_blank" for more information.

6. Utilisez les instructions suivantes pour créer une nouvelle table " interne " nommée **errorLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Ces instructions effectuent les opérations suivantes :

    * **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé **EXTERNAL** n'étant pas utilisé, il s'agit d'une table interne, stockée dans l'entrepôt de données Hive et gérée intégralement par Hive.

		> [AZURE.NOTE] Contrairement aux tables **EXTERNAL**, la suppression d'une table interne entraîne également la suppression des données sous-jacentes.

    * **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
    * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**
    * **SELECT * ** : sélectionne toutes les lignes de la nouvelle table **errorLogs**.

7. Utilisez l'identificateur de la tâche renvoyé pour vérifier l'état de la tâche. Une fois la tâche terminée, utilisez le xplat-cli, comme indiqué précédemment, pour télécharger et afficher les résultats. La sortie doit comporter trois lignes, qui contiennent toutes la valeur **[ERROR]**.


##<a id="summary"></a>Résumé

Comme indiqué dans ce document, vous pouvez utiliser des requêtes brutes HTTP pour exécuter, surveiller et afficher les résultats des tâches Hive sur votre cluster HDInsight.

Pour plus d'informations sur l'interface REST utilisée dans cet article, consultez la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">référence WebHCat</a>.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](../hdinsight-use-hive/)

Pour découvrir d'autres manières d'utiliser Hadoop sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](../hdinsight-use-pig/)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](../hdinsight-use-mapreduce/)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ documentation/articles/hdinsight-connect-excel-power-query/


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

<!--HONumber=45--> 
