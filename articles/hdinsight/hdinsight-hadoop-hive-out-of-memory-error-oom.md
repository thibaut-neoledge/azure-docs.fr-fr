---
title: "Erreur de mémoire insuffisante - Paramètres Hive | Microsoft Docs"
description: "Corrigez une erreur de mémoire insuffisante à partir d’une requête Hive dans Hadoop sous HDInsight. Le scénario client implique une requête sur de nombreuses tables de grande taille."
keywords: "erreur de mémoire insuffisante, OOM, paramètres Hive"
services: hdinsight
documentationcenter: 
author: rashimg
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: rashimg;jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bf0ff13a2d5ffc5bf0b07b80f482fc4144b0cd0f
ms.lasthandoff: 11/17/2016


---
# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Corriger une erreur de mémoire insuffisante avec les paramètres de mémoire Hive dans Hadoop sous Azure HDInsight
La réception d’une erreur de mémoire insuffisante lors de l’utilisation de Hive est un des problèmes courants rencontrés par nos clients. Cet article décrit un scénario client et les paramètres Hive recommandés pour la résolution du problème.

## <a name="scenario-hive-query-across-large-tables"></a>Scénario : requête Hive dans des tables de grande taille
Un client a exécuté la requête ci-dessous à l’aide de Hive.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Voici quelques caractéristiques de cette requête :

* T1 est un alias correspondant à une très grande table nommée TABLE1 qui comporte de nombreux  types de colonne au format de chaîne.
* Les autres tables ne sont pas si volumineuses, mais elles ont un grand nombre de colonnes.
* Toutes les tables sont associées les unes aux autres, parfois avec plusieurs colonnes dans TABLE1 et d’autres tables.

Lorsque le client a exécuté la requête à l’aide de Hive sur MapReduce sur un cluster A3 doté de 24 nœuds, la requête a été exécutée en environ 26 minutes. Le client a remarqué les messages d’avertissement suivants lorsque la requête a été exécutée à l’aide de Hive sur MapReduce :

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Étant donné que la requête s’est achevée au bout d’environ 26 minutes, le client a ignoré ces avertissements et a ensuite cherché à améliorer ses performances.

Le client a consulté la page [Optimisation des requêtes Hive pour Hadoop dans HDInsight](hdinsight-hadoop-optimize-hive-query.md)et a décidé d’utiliser le moteur d’exécution Tez. Une fois cette même requête exécutée avec le paramètre Tez, elle a fonctionné pendant 15 minutes puis a renvoyé l’erreur suivante :

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Le client a ensuite décidé d’utiliser une plus grande machine virtuelle (par exemple, D12) en pensant que celle-ci aurait davantage d’espace de tas. Mais le client a rencontré la même erreur. Le client a contacté l’équipe HDInsight pour que celle-ci l’aide à résoudre ce problème.

## <a name="debug-the-out-of-memory-oom-error"></a>Déboguer l’erreur de mémoire insuffisante
Nos équipes d’ingénierie et de support technique ont trouvé qu’un des problèmes à l’origine de l’erreur de mémoire insuffisante était [décrit dans Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306). À partir de la description disponible dans JIRA :

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

En consultant le fichier hive-site.xml, nous avons confirmé que **hive.auto.convert.join.noconditionaltask** a en effet la valeur **true** :

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

En nous basant sur l’avertissement et JIRA, notre hypothèse était que la commande Map Join était la cause de l’erreur de mémoire insuffisante d’espace de tas Java. Nous avons donc approfondi notre examen du problème.

Comme expliqué dans le billet de blog [Hadoop Yarn memory settings in HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), lorsque le moteur d’exécution Tez est utilisé, l’espace de tas utilisé appartient en fait au conteneur Tez. Voir l’image ci-dessous décrivant la mémoire de conteneur Tez.

![Diagramme de la mémoire du conteneur Tez : erreur de mémoire insuffisante dans Hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Comme le suggère le billet de blog, les deux paramètres de mémoire suivants définissent la mémoire de conteneur du tas : **hive.tez.container.size** et **hive.tez.java.opts**. D’après notre expérience, l’exception relative à une mémoire insuffisante ne signifie pas que la taille du conteneur est trop petite. Elle signifie que la taille du tas Java (hive.tez.java.opts) est trop petite. Par conséquent, lorsque vous voyez une erreur de mémoire insuffisante, vous pouvez essayer d’augmenter la valeur de **hive.tez.java.opts**. Si nécessaire, vous pouvez augmenter **hive.tez.container.size**. Le paramètre **java.opts** doit correspondre à environ 80 % de la taille de conteneur (**container.size**).

> [!NOTE]
> Le paramètre **hive.tez.java.opts** doit toujours être inférieur à **hive.tez.container.size**.
> 
> 

Comme un ordinateur D12 a une mémoire de 28 Go, nous avons décidé d’utiliser une taille de conteneur de 10 Go (10 240 Mo) et d’affecter la valeur 80 % à java.opts. Cette opération a été effectuée sur la console Hive à l’aide du paramètre ci-dessous :

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Avec ces paramètres, la requête s’est correctement exécutée en moins de dix minutes.

## <a name="conclusion-oom-errors-and-container-size"></a>Conclusion : erreurs de mémoire insuffisante et taille de conteneur
L’obtention d’une erreur de mémoire insuffisante ne signifie pas nécessairement que la taille du conteneur est insuffisante. Vous devez plutôt configurer les paramètres de mémoire afin que la taille du tas soit augmentée et qu’elle représente au moins 80 % de la taille de la mémoire du conteneur.


