---
title: Utilisation de MapReduce et de Curl avec Hadoop dans HDInsight | Microsoft Docs
description: "Apprenez à exécuter à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 41733f76e5f0c82c5321158d6a919f35f6faee3a
ms.lasthandoff: 03/21/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Exécution à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans ce document, vous allez apprendre à utiliser Curl pour exécuter des tâches MapReduce sur un Hadoop sur le cluster HDInsight.

Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des requêtes HTTP brutes pour exécuter des tâches MapReduce. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [!NOTE]
> Si vous vous êtes déjà familiarisé avec l'utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez [Ce qu'il faut savoir sur Hadoop dans HDInsight sous Linux](hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Exécution de tâches MapReduce à l'aide de Curl
> [!NOTE]
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les requêtes en fournissant le nom d’utilisateur et le mot de passe d’administrateur de cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI utilisée pour envoyer les requêtes au serveur.
> 
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
> 
> L’API REST est sécurisée à l’aide de l’ [authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les requêtes à l’aide de HTTPS pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.
> 
> 

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Vous devez recevoir une réponse ayant l'aspect suivant :
   
        {"status":"ok","version":"v1"}
   
    Les paramètres utilisés dans cette commande sont les suivants :
   
   * **-u**: indique le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
   * **-G**: indique qu’il s’agit d’une requête GET.
     
     Le début de l’URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes.
2. Pour envoyer une tâche MapReduce, utilisez la commande suivante :
   
        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
   
    La fin de l’URI (/mapreduce/jar) indique à WebHCat que cette demande lance une tâche MapReduce à partir d’une classe dans un fichier jar. Les paramètres utilisés dans cette commande sont les suivants :
   
   * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.
     
     * **user.name**: l’utilisateur qui exécute la commande
     * **jar**: l’emplacement du fichier jar contenant la classe à exécuter
     * **class**: la classe contenant la logique MapReduce
     * **arg**: les arguments à transmettre à la tâche MapReduce ; dans ce cas, le fichier texte d'entrée et le répertoire utilisés pour la sortie
     
     Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche :
     
       {"id":"job_1415651640909_0026"}
3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le JOBID sera `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Si la tâche est terminée, l’état est « TERMINÉ ».
   
   > [!NOTE]
   > Cette demande Curl renvoie un document JSON avec des informations sur la tâche ; jq est utilisé pour récupérer la valeur de statut.
   > 
   > 
4. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage d’objets blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, **wasbs:///example/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **example/curl** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [interface de ligne de commande Azure](../cli-install-nodejs.md). Par exemple, pour répertorier les fichiers dans **example/curl**, utilisez la commande suivante :

    azure storage blob list <container-name> example/curl

Pour télécharger un fichier, utilisez ce qui suit :

    azure storage blob download <container-name> <blob-name> <destination-file>

> [!NOTE]
> Vous devez spécifier le nom du compte de stockage qui contient l’objet blob à l’aide des paramètres `-a` et `-k`, ou définir les variables d’environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**. Voir [Téléchargement des données vers HDInsight](hdinsight-upload-data.md) pour plus d'informations.
> 
> 

## <a id="summary"></a>Résumé
Comme illustré dans ce document, vous pouvez utiliser des requêtes HTTP brutes pour exécuter, surveiller et afficher les résultats de tâches Hive sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)


