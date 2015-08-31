<properties
   pageTitle="Utilisation de Hadoop Pig avec Curl dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser Curl pour exécuter des tâches Pig Latin sur un cluster Hadoop dans HDInsight Azure."
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

#Exécution à distance des tâches Pig avec Hadoop sur HDInsight à l’aide de Curl

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dans ce document, vous allez apprendre à utiliser Curl pour exécuter des tâches Pig Latin sur un cluster HDInsight Azure. Le langage de programmation Pig Latin permet de décrire les transformations appliquées aux données d’entrée pour produire le résultat souhaité.

Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des demandes HTTP brutes pour exécuter, analyser et récupérer des tâches Pig. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE]Si vous connaissez déjà l’utilisation de serveurs Hadoop basés sur Linux, mais pas HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécution de tâches Pig à l’aide de Curl

> [AZURE.NOTE]Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur pour le cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
>
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
>
> L’API REST est sécurisée à l’aide de l’[authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous devez recevoir une réponse ayant l'aspect suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande
    * **-G** : indique qu’il s’agit d’une requête GET.

    Le début de l’URL, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande doit retourner le statut de WebHCat (également appelé Templeton) au serveur.

2. Utilisez le code suivant pour soumettre une tâche Pig Latin au cluster :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

        * **user.name** : l’utilisateur qui exécute la commande
        * **execute** : les instructions Pig Latin à exécuter
        * **statusdir** : le répertoire où seront enregistrés les statuts de cette tâche

    > [AZURE.NOTE]Notez que les espaces dans les instructions Pig Latin sont remplacées par le caractère `+` avec Curl.

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche, par exemple :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le **JOBID** est `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si la tâche est terminée, l’état est **TERMINÉ**.

    > [AZURE.NOTE]Cette requête Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche et jq est utilisé pour récupérer uniquement la valeur d’état.

##<a id="results"></a>Affichage des résultats

Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage d’objets blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, ****wasb:///example/pigcurl**. Cette adresse stocke la sortie de la tâche dans le répertoire **example/pigcurl** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez répertorier et télécharger ces fichiers à l'aide de la [CLI Azure pour Mac, Linux et Windows](../xplat-cli.md). Par exemple, pour répertorier les fichiers dans **example/pigcurl**, utilisez la commande suivante :

	azure storage blob list <container-name> example/pigcurl

Pour télécharger un fichier, utilisez ce qui suit :

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Vous devez spécifier le nom du compte de stockage qui contient l’objet blob à l’aide des paramètres `-a` et `-k` ou définir les variables d’environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**.

##<a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser les demandes HTTP brutes pour exécuter, surveiller et afficher les résultats de tâches Pig sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->