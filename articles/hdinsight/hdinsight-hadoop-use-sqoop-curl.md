---
title: Utiliser Hadoop Sqoop avec Curl dans HDInsight | Microsoft Docs
description: "Découvrez comment transmettre à distance des travaux Sqoop vers HDInsight à l’aide de Curl."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: eabcd8b70411a915722a2f1b8e8b70271cd8c7c2
ms.contentlocale: fr-fr
ms.lasthandoff: 03/21/2017


---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Exécution de travaux Sqoop avec Hadoop dans HDInsight via Curl
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Apprenez à utiliser Curl pour exécuter des tâches Sqoop sur un cluster Hadoop dans HDInsight.

Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des demandes HTTP brutes pour exécuter, analyser et récupérer des travaux Sqoop. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

> [!NOTE]
> Si vous connaissez déjà l’utilisation de serveurs Hadoop basés sur Linux, mais pas HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md).
> 
> 

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight (Linux ou Windows)
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a name="submit-sqoop-jobs-by-using-curl"></a>Envoi de travaux Sqoop avec Curl
> [!NOTE]
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
> 
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
> 
> L’API REST est sécurisée à l’aide de l’ [authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.
> 
> 

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Vous devez recevoir une réponse ayant l'aspect suivant :
   
        {"status":"ok","version":"v1"}
   
    Les paramètres utilisés dans cette commande sont les suivants :
   
   * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
   * **-G** : indique qu’il s’agit d’une demande GET.
     
     Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande doit renvoyer le statut de WebHCat (également appelé Templeton) au serveur. 
2. Pour envoyer un travail Sqoop, utilisez la commande suivante :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

        * **user.name** : l’utilisateur qui exécute la commande.

        * **command** : commande Sqoop à exécuter.

        * **statusdir** : le répertoire où seront enregistrés les statuts de cette tâche.

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche.

        {"id":"job_1415651640909_0026"}

1. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le **JOBID** est `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Si le travail est terminé, l’état est **TERMINÉ**.
   
   > [!NOTE]
   > Cette demande Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche ; jq est utilisé pour récupérer uniquement la valeur de statut.
   > 
   > 
2. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, **wasb:///exemple/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/curl** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.
   
    Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [interface de ligne de commande Azure](../cli-install-nodejs.md). Par exemple, pour répertorier les fichiers dans **exemple/curl**, utilisez la commande suivante :
   
        azure storage blob list <container-name> example/curl
   
    Pour télécharger un fichier, utilisez ce qui suit :
   
        azure storage blob download <container-name> <blob-name> <destination-file>
   
   > [!NOTE]
   > Vous devez spécifier le nom du compte de stockage qui contient l’objet blob à l’aide des paramètres `-a` et `-k`, ou définir les variables d’environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**. Consultez <a href="hdinsight-upload-data.md" target="_blank" pour plus d'informations.
   > 
   > 

## <a name="limitations"></a>Limitations
* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend pas en charge les insertions en bloc.
* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="summary"></a>Résumé
Comme illustré dans ce document, vous pouvez utiliser une demande HTTP brute pour exécuter, surveiller et afficher les résultats des travaux Sqoop sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez le document <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API guide</a> (Guide de l’API REST Sqoop).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utilisation de Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

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




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx



