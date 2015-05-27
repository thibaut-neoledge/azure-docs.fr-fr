<properties
   pageTitle="Utilisation de MapReduce et de Curl avec Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à exécuter à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl."
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
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#Exécution à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Dans ce document, vous allez apprendre à utiliser Curl pour exécuter des tâches MapReduce sur un Hadoop sur le cluster HDInsight.

Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des requêtes HTTP brutes pour exécuter des tâches MapReduce. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE]Si vous vous êtes déjà familiarisé avec l'utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez [Ce qu'il faut savoir sur Hadoop dans HDInsight sous Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight (Linux ou Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

##<a id="curl"></a>Exécution de tâches MapReduce à l'aide de Curl

> [AZURE.NOTE]Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les requêtes en fournissant le nom d’utilisateur et le mot de passe d’administrateur de cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI utilisée pour envoyer les requêtes au serveur.
>
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
>
> L'API REST est sécurisée à l'aide de l'<a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">authentification de base</a>. Vous devez toujours effectuer les requêtes à l’aide de HTTPS pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devez recevoir une réponse ayant l'aspect suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** : indique le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
    * **-G** : indique qu’il s’agit d’une requête GET.

    Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les demandes.

2. Pour envoyer une tâche MapReduce, utilisez la commande suivante :

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    La fin de l’URI (/mapreduce/jar) indique à WebHCat que cette demande lance une tâche MapReduce à partir d’une classe dans un fichier jar. Les paramètres utilisés dans cette commande sont les suivants :

	* **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

        * **user.name** : l’utilisateur qui exécute la commande
        * **jar** : l’emplacement du fichier jar contenant la classe à exécuter
        * **class** : la classe contenant la logique MapReduce
        * **arg**: les arguments à transmettre à la tâche MapReduce ; dans ce cas, le fichier texte d'entrée et le répertoire utilisés pour la sortie

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le JOBID sera `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si la tâche est terminée, l’état est « TERMINÉ ».

    > [AZURE.NOTE]Cette demande Curl renvoie un document JSON avec des informations sur la tâche ; jq est utilisé pour récupérer la valeur de statut.

4. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage d’objets blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, **wasb:///example/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **example/curl** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez répertorier et télécharger ces fichiers à l'aide de la [CLI Azure pour Mac, Linux et Windows](xplat-cli.md). Par exemple, pour répertorier les fichiers dans **example/curl**, utilisez la commande suivante :

	azure storage blob list <container-name> example/curl

Pour télécharger un fichier, utilisez ce qui suit :

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Vous devez spécifier le nom du compte de stockage qui contient l’objet blob à l’aide des paramètres `-a` et `-k` ou définir les variables d’environnement **AZURE_STORAGE_ACCOUNT** et **AZURE_STORAGE_ACCESS_KEY**. Voir [Téléchargement des données vers HDInsight](hdinsight-upload-data.md) pour plus d'informations.

##<a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser des requêtes HTTP brutes pour exécuter, surveiller et afficher les résultats de tâches Hive sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

<!--HONumber=54-->