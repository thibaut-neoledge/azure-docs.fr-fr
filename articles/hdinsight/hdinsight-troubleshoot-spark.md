---
title: "Résolution de problèmes Spark - Azure HDInsight | Microsoft Docs"
description: "Utilisez le FAQ Stark pour obtenir des réponses aux questions courantes sur l’utilisation de Spark sur la plateforme Azure HDInsight."
keywords: "Azure HDInsight, Spark, FAQ, guide de résolution des problèmes, problèmes courants, configuration d’application, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---

# <a name="spark-troubleshooting"></a>Résolution de problèmes Spark

Cet article décrit les principaux problèmes rencontrés lors de l’utilisation de charges utiles Spark dans Apache Ambari, ainsi que les étapes à suivre pour les résoudre.

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>Comment configurer une application Spark sur des clusters via Ambari ?

### <a name="issue"></a>Problème :

Vous avez besoin de configurer dans Ambari la quantité de mémoire et le nombre de cœurs qu’une application Spark peut utiliser.  

### <a name="resolution-steps"></a>Étapes de résolution : 

Cette procédure fait appel à des valeurs de configuration précédemment définies dans les clusters HDInsight Spark. Pour connaître les configurations concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#why-did-my-spark-application-fail-with-an-outofmemoryerror).

1. Dans la liste des clusters, choisissez **Spark2**.

    ![Sélectionnez le cluster dans la liste](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. Sélectionnez l’onglet **Configurations** .

    ![Choisissez l’onglet Configs (Configurations)](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. Dans la liste des configurations, choisissez **Custom-spark2-defaults**.

    ![Choisissez custom-spark-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. Recherchez le paramètre de valeur que vous avez besoin d’ajuster, par exemple **spark.executor.memory**. Dans le cas présent, la valeur de m4608 est trop élevée.

    ![Sélectionnez le champ spark.executor.memory](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. Définissez la valeur recommandée. Dans le cas présent, une valeur de 2048m est recommandée pour ce paramètre.

    ![Remplacez la valeur par 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. Enregistrez la valeur du paramètre, puis enregistrez la configuration. 

    Cliquez sur **Save** dans la barre d'outils.

    ![Enregistrez le paramètre et la configuration](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    Si des configurations requièrent votre attention, un message s’affiche. Prenez note des configurations concernées, puis cliquez sur **Proceed Anyway** (Continuer). 

    ![Cliquez sur Proceed Anyway (Continuer)](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    Écrivez une note concernant les modifications de configuration apportées, puis cliquez sur **Save** (Enregistrer).

    ![Saisissez une note concernant les modifications apportées](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. À chaque fois que vous enregistrez une configuration, vous êtes invité à redémarrer le service. Cliquez sur **Restart (Redémarrer)**.

    ![Cliquez sur Restart (Redémarrer).](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    Confirmez le redémarrage.

    ![Cliquez sur Confirm Restart All (Confirmer le redémarrage)](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    Vous pouvez examiner les processus en cours d’exécution.

    ![Examinez les processus en cours d’exécution](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. Vous pouvez également ajouter des configurations. Dans la liste des configurations, choisissez **Custom-spark2-defaults**, comme à l’étape 3, puis sélectionnez **Add Property** (Ajouter une propriété).

    ![Cliquez sur Add Property (Ajouter une propriété)](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. Définissez une nouvelle propriété. Vous pouvez définir une propriété unique à l’aide d’une boîte de dialogue pour des paramètres spécifiques, tels que le type de données, ou définir plusieurs propriétés avec une définition par ligne. 

    Dans cet exemple, la propriété **spark.driver.memory** est définie avec une valeur de 4g.

    ![Définissez une nouvelle propriété](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. Enregistrez la configuration et redémarrez le service en suivant la procédure décrite aux étapes 6 et 7.

Ces modifications s’appliquent à l’ensemble du cluster, mais elles peuvent être remplacées au moment de l’envoi de travaux Spark.

### <a name="further-reading"></a>Pour aller plus loin :

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>Comment configurer une application Spark sur des clusters via un bloc-notes Jupyter ?

### <a name="issue"></a>Problème :

Vous avez besoin de configurer la quantité de mémoire et le nombre de cœurs qu’une application Spark peut utiliser sur des clusters HDInsight lors de l’utilisation d’un bloc-notes Jupyter. 

1. Pour connaître les configurations concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#spark-application-failure-outofmemory).
1.  Spécifiez les configurations Spark dans un format JSON valide dans la première cellule du bloc-notes Jupyter, après la directive %%configure (modifiez les valeurs selon vos besoins) : 

>![Ajoutez une configuration](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>Pour aller plus loin :

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>Comment configurer une application Spark sur des clusters via Livy ?

### <a name="issue"></a>Problème :

Vous avez besoin de configurer la quantité de mémoire et le nombre de cœurs qu’une application Spark peut utiliser sur des clusters HDInsight au moment de l’envoi via Livy. 

1. Pour connaître les configurations concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#spark-application-failure-outofmemory).
1. Envoyez l’application Spark à Livy à l’aide d’un client REST tel que CURL avec une commande semblable à celle-ci (modifiez les valeurs selon vos besoins) :

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>Pour aller plus loin :

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>Comment configurer une application Spark sur des clusters via spark-submit ?

### <a name="issue"></a>Problème : 

Vous avez besoin de configurer la quantité de mémoire et le nombre de cœurs qu’une application Spark peut utiliser sur des clusters HDInsight au moment de l’envoi via spark-submit.

1. Pour connaître les configurations concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#spark-application-failure-outofmemory).
1. Lancez spark-shell avec une commande semblable à celle-ci (modifiez les valeurs des configurations selon vos besoins) : 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>Pour aller plus loin :

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?

### <a name="error"></a>Error:

L’application Spark échoue avec l’exception OutOfMemoryError.

### <a name="detailed-description"></a>Description détaillée :

L’application Spark échoue avec les types suivants d’exceptions non interceptées.  
```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Cause probable :

Cette exception est souvent générée lorsque trop peu de mémoire de tas est allouée aux machines virtuelles Java qui sont lancées en tant qu’exécuteurs ou pilotes dans le cadre de l’application Spark. 

### <a name="resolution-steps"></a>Étapes de résolution :

1. Déterminez le volume maximal de données que l’application Spark va traiter. Pour estimer ce volume, basez-vous sur le volume maximal des données d’entrée, des données intermédiaires produites par la transformation des données d’entrée et des données de sortie produites à partir des données intermédiaires. Vous pouvez également utiliser un processus itératif si une estimation initiale formelle n’est pas possible. 
1. Assurez-vous que le cluster HDInsight à utiliser dispose de suffisamment de ressources en termes de mémoire et de cœurs pour prendre en charge l’application Spark. Pour le vérifier, accédez à la section Mesures de cluster de l’interface utilisateur YARN du cluster et comparez les valeurs Mémoire utilisée/Mémoire totale et VCores utilisés/Total des VCores .

1. Définissez les configurations Spark suivantes de manière à ce que les valeurs ne dépassent pas 90 % de la mémoire et du nombre de cœurs disponibles tel qu’affiché dans YARN pour la mémoire requise de l’application Spark : 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

Mémoire totale utilisée par tous les exécuteurs = 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
Mémoire totale utilisée par le pilote = 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>Pour aller plus loin :

- [Spark memory management overview](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview) (Vue d’ensemble de la gestion de la mémoire dans Spark)
- [Debugging Spark application on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/) (Débogage d’une application Spark sur des clusters HDInsight)










