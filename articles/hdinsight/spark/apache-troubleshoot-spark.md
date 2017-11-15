---
title: "Résolution de problèmes Spark à l’aide d’Azure HDInsight | Microsoft Docs"
description: "Obtenez les réponses aux questions courantes sur l’utilisation d’Apache Spark et d’Azure HDInsight."
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
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: b75aca7d9f78cb425099cc33034b44a80d7b6b81
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Résolution de problèmes Spark à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache Spark dans Apache Ambari, et leur résolution.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Comment configurer une application Spark sur des clusters via Ambari ?

### <a name="resolution-steps"></a>Étapes de résolution

Cette procédure fait appel à des valeurs de configuration précédemment définies dans HDInsight. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Dans la liste de clusters, sélectionnez **Spark2**.

    ![Sélectionnez le cluster dans la liste](./media/apache-troubleshoot-spark/update-config-1.png)

2. Sélectionnez l'onglet **Configurations** .

    ![Sélectionnez l’onglet Configurations](./media/apache-troubleshoot-spark/update-config-2.png)

3. Dans la liste des configurations, sélectionnez **Custom-spark2-defaults**.

    ![Sélectionnez custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-3.png)

4. Recherchez le paramètre de valeur que vous avez besoin d’ajuster, par exemple **spark.executor.memory**. Dans le cas présent, la valeur de **4608m** est trop élevée.

    ![Sélectionnez le champ spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Définissez la valeur sur le paramètre recommandé. La valeur **2048m** est recommandée pour ce paramètre.

    ![Remplacez la valeur par 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Enregistrez la valeur, puis la configuration. Dans la barre d’outils, sélectionnez **Enregistrer**.

    ![Enregistrez le paramètre et la configuration](./media/apache-troubleshoot-spark/update-config-6a.png)

    Si des configurations requièrent votre attention, un message s’affiche. Notez les éléments, puis sélectionnez **Proceed Anyway** (Continuer). 

    ![Sélectionnez Proceed Anyway (Continuer)](./media/apache-troubleshoot-spark/update-config-6b.png)

    Notez les modifications apportées à la configuration, puis sélectionnez **Enregistrer**.

    ![Saisissez une note concernant les modifications apportées](./media/apache-troubleshoot-spark/update-config-6c.png)

7. À chaque fois que vous enregistrez une configuration, vous êtes invité à redémarrer le service. Sélectionnez **Redémarrer**.

    ![Sélectionnez Redémarrer](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirmez le redémarrage.

    ![Sélectionnez Confirm Restart All (Confirmer le redémarrage)](./media/apache-troubleshoot-spark/update-config-7b.png)

    Vous pouvez examiner les processus en cours d’exécution.

    ![Examinez les processus en cours d’exécution](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Vous pouvez ajouter des configurations. Dans la liste des configurations, sélectionnez **Custom-spark2-defaults**, puis **Ajouter une propriété**.

    ![Sélectionnez Ajouter une propriété](./media/apache-troubleshoot-spark/update-config-8.png)

9. Définissez une nouvelle propriété. Vous pouvez définir une propriété unique via une boîte de dialogue de paramètres spécifiques tels que le type de données. Vous pouvez également définir plusieurs propriétés en utilisant une définition par ligne. 

    Dans cet exemple, la propriété **spark.driver.memory** est définie avec une valeur de **4g**.

    ![Définissez une nouvelle propriété](./media/apache-troubleshoot-spark/update-config-9.png)

10. Enregistrez la configuration et redémarrez le service en suivant la procédure décrite aux étapes 6 et 7.

Ces modifications s’appliquent à l’ensemble du cluster, mais elles peuvent être remplacées au moment de l’envoi du travail Spark.

### <a name="additional-reading"></a>Documentation supplémentaire

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Comment configurer une application Spark sur des clusters via un bloc-notes Jupyter ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Dans la première cellule du bloc-notes Jupyter, après la directive **%%configure**, spécifiez les configurations Spark dans un format JSON valide. Modifiez les valeurs si nécessaire :

    ![Ajoutez une configuration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Documentation supplémentaire

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Comment configurer une application Spark sur des clusters via Livy ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Soumettez la demande de l’application Spark à Livy à l’aide d’un client REST comme cURL. Utilisez une commande similaire à la suivante. Modifiez les valeurs si nécessaire :

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Documentation supplémentaire

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Comment configurer une application Spark sur des clusters via spark-submit ?

### <a name="resolution-steps"></a>Étapes de résolution

1. Pour connaître les configurations Spark concernées et les valeurs à définir, reportez-vous à la rubrique [À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Lancez spark-shell à l’aide d’une commande semblable à la suivante. Modifiez la valeur des configurations selon les besoins : 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Documentation supplémentaire

[Spark job submission on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envoi de travaux Spark sur des clusters HDInsight)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>À quoi est dû l’échec d’une application Spark avec l’exception OutofMemoryError ?

### <a name="detailed-description"></a>Description détaillée

L’application Spark échoue avec les types suivants d’exceptions non interceptées :

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

### <a name="probable-cause"></a>Cause probable

La cause la plus probable de cette exception est que le segment de mémoire allouée aux machines virtuelles Java (JVM) est insuffisant. Ces JVM sont lancées en tant qu’exécuteurs ou pilotes dans le cadre de l’application Spark. 

### <a name="resolution-steps"></a>Étapes de résolution

1. Déterminez le volume maximal de données que l’application Spark peut gérer. Pour estimer ce volume, basez-vous sur le volume maximal des données d’entrée, des données intermédiaires produites par la transformation des données d’entrée et des données de sortie produites lorsque l’application transforme davantage les données intermédiaires. Ce processus peut être réitéré si vous ne parvenez pas à effectuer une estimation formelle initiale. 

2. Assurez-vous que le cluster HDInsight à utiliser dispose de suffisamment de ressources en termes de mémoire et de cœurs pour prendre en charge l’application Spark. Pour le vérifier, accédez à la section des mesures de cluster de l’interface utilisateur YARN et comparez les valeurs **Mémoire utilisée** et **Mémoire totale**, et les valeurs **VCores utilisés** et **Total des VCores**.

3. Définissez les configurations Spark suivantes sur les valeurs appropriées, qui ne doivent pas dépasser 90 % de la mémoire et des cœurs disponibles. Les valeurs doivent correspondre à la mémoire requise pour l’application Spark : 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Pour calculer la mémoire totale utilisée par tous les exécuteurs : 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Pour calculer la mémoire totale utilisée par le pilote :
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Documentation supplémentaire

- [Spark memory management overview](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview) (Vue d’ensemble de la gestion de la mémoire dans Spark)
- [Debug a Spark application on an HDInsight cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/) (Déboguer une application Spark sur un cluster HDInsight)


### <a name="see-also"></a>Voir aussi
[Résoudre des problèmes à l’aide d’Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

