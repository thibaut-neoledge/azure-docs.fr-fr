<properties
   pageTitle="Utiliser Hadoop Hive avec Curl dans HDInsight | Microsoft Azure"
   description="Découvrez comment transmettre à distance des tâches Pig vers HDInsight à l'aide de Curl."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#Exécution de requêtes Hive avec Hadoop dans HDInsight via Curl

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans ce document, vous découvrirez comment utiliser Curl pour exécuter des requêtes Hive sur un cluster Hadoop sur Azure HDInsight.

Curl est utilisé pour vous montrer comment vous pouvez interagir avec HDInsight à l'aide de requêtes brutes HTTP pour exécuter, surveiller et récupérer les résultats des requêtes Hive. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l'utilisation de serveurs Hadoop sur Linux, mais que vous découvrez HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight (Linux ou Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécution de requêtes Hive à l'aide de Curl

> [AZURE.NOTE] Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
>
> L’API REST est sécurisée à l’aide de l’[authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devez recevoir une réponse ayant l'aspect suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
    * **-G** : indique qu’il s’agit d’une demande GET.

    Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes. Le chemin d’accès, **/status, indique que la demande doit renvoyer le statut de WebHCat (également appelé Templeton) au serveur. Vous pouvez également prendre connaissance de la version de Hive à l'aide de la commande suivante :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Vous devez recevoir une réponse ayant l'aspect suivant :

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Utilisez la commande suivante pour créer une nouvelle table nommée **log4jLogs** :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

        * **user.name** : l’utilisateur qui exécute la commande.

        * **execute** : les instructions HiveQL qui doivent être exécutées.

        * **statusdir** : le répertoire où seront enregistrés les statuts de cette tâche.

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.

    * **CREATE EXTERNAL TABLE** : crée une table externe dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

		> [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
		>
		> La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

    * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.

    * **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire example/data) et qu'elles sont stockées sous forme de texte.

    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit retourner la valeur **3**, car trois lignes contiennent cette valeur.

    > [AZURE.NOTE] Notez que les espaces entre les instructions HiveQL sont remplacés par le caractère `+` lorsqu'ils sont utilisés avec Curl. Les valeurs citées qui contiennent un espace, tel que le séparateur, ne doivent pas être remplacées par `+`.

    * **INPUT\_\_FILE\_\_NAME LIKE '%25.log'** : ceci limite la recherche aux seuls fichiers se terminant par .log. Si cette valeur n'est pas présente, Hive tente de rechercher tous les fichiers dans ce répertoire et ses sous-répertoires, y compris les fichiers qui ne correspondent pas au schéma de colonne défini pour cette table.

    > [AZURE.NOTE] Notez que %25 est la forme codée de l'URL de %, donc la condition réelle est `like '%.log'`. La valeur % doit être une URL encodée, car elle est traitée comme un caractère spécial dans les URL.

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche.

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le **JOBID** est `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si le travail est terminé, l’état est **TERMINÉ**.

    > [AZURE.NOTE] Cette demande Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche ; jq est utilisé pour récupérer uniquement la valeur de statut.

4. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage d’objets blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, **wasbs:///example/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/curl sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

    Vous pouvez répertorier et télécharger ces fichiers à l’aide de l'[interface de ligne de commande Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers dans **exemple/curl**, utilisez la commande suivante :

		azure storage blob list <container-name> example/curl

	Pour télécharger un fichier, utilisez ce qui suit :

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Vous devez spécifier le nom du compte de stockage qui contient l’objet blob à l’aide des paramètres `-a` et `-k` ou définir les variables d’environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**. Consultez <a href="hdinsight-upload-data.md" target="\_blank" pour plus d'informations.

6. Utilisez les instructions suivantes pour créer une nouvelle table « interne » nommée **errorLogs** :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Ces instructions effectuent les opérations suivantes :

    * **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive.

		> [AZURE.NOTE] Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

    * **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
    * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.
    * **SELECT** : sélectionne toutes les lignes de la nouvelle table **errorLogs**.

7. Utilisez l'identificateur de la tâche renvoyé pour vérifier l'état de la tâche. Une fois la tâche terminée, utilisez la CLI Azure pour Mac, Linux et Windows, comme indiqué précédemment, pour télécharger et afficher les résultats. La sortie doit comporter trois lignes, qui contiennent toutes la valeur **ERROR**.


##<a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser une demande HTTP brute pour exécuter, surveiller et afficher les résultats de tâches Hive sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Référence WebHCat</a>.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utiliser l'interface utilisateur Tez sur HDInsight Windows](hdinsight-debug-tez-ui.md)

* [Utilisez la vue Tez Ambari sur HDInsight Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0914_2016-->