---
title: "Gérer les ressources du cluster Apache Spark dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser la gestion des ressources pour les clusters Spark sur Azure HDInsight pour obtenir de meilleures performances."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 68972d9422dd0861318b0f26a4543f7596d9a680


---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gérer les ressources du cluster Apache Spark dans Azure HDInsight 

Dans cet article, vous allez découvrir comment accéder aux interfaces comme l’IU d’Ambari et l’IU de Yarn, ainsi qu’au serveur d’historique Spark associé à votre cluster Spark. Vous allez également découvrir comment ajuster la configuration du cluster afin d’optimiser les performances.

**Configuration requise :**

Vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-launch-the-ambari-web-ui"></a>Comment lancer l’interface utilisateur web Ambari ?
1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.
2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark.

    ![Lancer Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Démarrer le gestionnaire de ressources")
3. Cela doit lancer l’interface utilisateur web Ambari comme indiqué ci-dessous.

    ![Interface utilisateur web Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Interface utilisateur web Ambari")   

## <a name="how-do-i-launch-the-spark-history-server"></a>Comment lancer le serveur d’historique Spark ?
1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil).
2. Dans le panneau du cluster, sous **Liens rapides**, cliquez sur **Tableau de bord du cluster**. Dans le panneau **Tableau de bord du cluster** , cliquez sur **Serveur d’historique Spark**.

    ![Serveur d’historique Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Serveur d’historique Spark")

    Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark.

## <a name="how-do-i-launch-the-yarn-ui"></a>Comment lancer l’interface utilisateur web Yarn ?
Vous pouvez utiliser l’interface utilisateur YARN pour surveiller les applications en cours d’exécution sur le cluster Spark.

1. Dans le panneau du cluster, cliquez sur **Tableau de bord du cluster**, puis sur **YARN**.

    ![Lancer l’interface utilisateur Yarn](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Vous pouvez également lancer l’interface utilisateur de YARN à partir de celle d’Ambari. Pour lancer l’interface utilisateur d’Ambari, dans le panneau du cluster, cliquez sur **Tableau de bord du cluster**, puis sur **Tableau de bord de cluster HDInsight**. À partir de l’interface utilisateur d’Ambari, cliquez successivement sur **YARN**, **Quick Links** (Liens rapides), le gestionnaire de ressources actif et **ResourceManager UI** (IU de ResourceManager).
   >
   >

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>Quelle est la configuration de cluster optimale pour l’exécution des applications Spark ?
Les trois paramètres clés pouvant être utilisés pour la configuration de Spark selon la configuration requise pour l’application sont `spark.executor.instances`, `spark.executor.cores` et `spark.executor.memory`. Un exécuteur est un processus lancé pour une application Spark. Il s’exécute sur le nœud de travail et est chargé d’effectuer les tâches de l’application. Le nombre d’exécuteurs par défaut et les tailles d’exécuteur de chaque cluster sont calculés en fonction du nombre de nœuds de travail et de leur taille. Ils sont stockés dans `spark-defaults.conf` sur les nœuds principaux du cluster.

Les trois paramètres de configuration peuvent être configurés au niveau du cluster (pour toutes les applications qui s’exécutent sur le cluster) ou spécifiés pour chaque application.

### <a name="change-the-parameters-using-ambari-ui"></a>Modifier les paramètres à l’aide de l’interface utilisateur d’Ambari
1. À partir de l’interface utilisateur d’Ambari, cliquez sur **Spark**, puis sur **Configs (Configurations)**, puis développez **Custom spark-defaults (Personnaliser les valeurs Spark par défaut)**.

    ![Définir des paramètres à l’aide d’Ambari](./media/hdinsight-apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Les valeurs par défaut conviennent si vous souhaitez exécuter simultanément 4 applications Spark sur le cluster. Vous pouvez modifier ces valeurs dans l’interface utilisateur, comme indiqué ci-dessous.

    ![Définir des paramètres à l’aide d’Ambari](./media/hdinsight-apache-spark-resource-manager/set-executor-parameters.png)
3. Cliquez sur **Save (Enregistrer)** pour enregistrer les modifications de la configuration. En haut de la page, vous êtes invité à redémarrer tous les services concernés. Cliquez sur **Restart (Redémarrer)**.

    ![Redémarrer les services](./media/hdinsight-apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Modifier les paramètres d’une application exécutée dans un bloc-notes Jupyter
Pour les applications exécutées dans le bloc-notes Jupyter, vous pouvez utiliser la commande magique `%%configure` pour procéder aux modifications de la configuration. Dans l’idéal, vous devez apporter ces modifications au début de l’application, avant d’exécuter la première cellule de code. De ce fait, la configuration est appliquée à la session Livy lors de sa création. Si vous souhaitez modifier la configuration ultérieurement dans l’application, vous devez utiliser le paramètre `-f` . Néanmoins, en procédant ainsi, toute la progression de l’application sera perdue.

L’extrait de code ci-dessous montre comment modifier la configuration d’une application exécutée dans Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, “numExecutors”:10}

Les paramètres de configuration doivent être passés en tant que chaîne JSON et spécifiés sur la ligne suivant la commande magique, comme indiqué dans l’exemple de colonne.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Modifier les paramètres d’une application soumise à l’aide du script spark-submit
La commande suivante est un exemple de modification des paramètres de configuration d’une application de lot soumise à l’aide de `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Modifier les paramètres d’une application soumise à l’aide de cURL
La commande suivante est un exemple de modification des paramètres de configuration d’une application de lot soumise à l’aide de cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>Comment modifier ces paramètres sur un serveur Thrift Spark ?
Le serveur Thrift Spark fournit un accès JDBC/ODBC à un cluster Spark et est utilisé pour les requêtes SQL Spark. Les outils tels que Power BI, Tableau, etc. utilisent le protocole ODBC pour communiquer avec le serveur Thrift Spark afin d’exécuter des requêtes SQL Spark en tant qu’application Spark. Lors de la création d’un cluster Spark, deux instances du serveur Thrift Spark sont démarrées, une par nœud principal. Chaque serveur Thrift Spark apparaît en tant qu’application Spark dans l’interface utilisateur de YARN.

Le serveur Thrift Spark utilise l’allocation d’exécuteur dynamique de Spark. Le code `spark.executor.instances` n’est donc pas utilisé. En revanche, le serveur Thrift Spark utilise `spark.dynamicAllocation.minExecutors` et `spark.dynamicAllocation.maxExecutors` pour indiquer le nombre d’exécuteurs. Les paramètres de configuration `spark.executor.cores` et `spark.executor.memory` permettent de modifier la taille de l’exécuteur. Vous pouvez modifier ces paramètres comme indiqué ci-dessous.

* Développez la catégorie **Advanced spark-thrift-sparkconf** pour mettre à jour les paramètres `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` et `spark.executor.memory`.

    ![Configurer le serveur Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-1.png)    
* Développez la catégorie **Custom spark-thrift-sparkconf** pour mettre à jour le paramètre `spark.executor.cores`.

    ![Configurer le serveur Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Comment modifier la mémoire du pilote du serveur Thrift Spark ?
La mémoire du pilote du serveur Thrift Spark est configurée sur 25 % de la taille de la mémoire RAM du nœud principal, sous réserve que la taille totale de la mémoire RAM du nœud principal soit supérieure à 14 Go. Vous pouvez utiliser l’interface utilisateur d’Ambari pour modifier la configuration de la mémoire du pilote, comme indiqué ci-dessous.

* À partir de l’interface utilisateur d’Ambari, cliquez sur **Spark**, puis sur **Configs (Configurations)**, développez **Advanced spark-env**, puis indiquez la valeur de **spark_thrift_cmd_opts**.

    ![Configurer la mémoire RAM du serveur Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>Je n’utilise pas d’outils décisionnels avec le cluster Spark. Comment reprendre des ressources ?
Étant donné que nous utilisons l’allocation dynamique de Spark, les seules ressources consommées par le serveur Thrift sont celles des deux applications maîtres. Pour libérer ces ressources, vous devez arrêter l’exécution des services du serveur Thrift sur le cluster.

1. À partir de l’interface utilisateur d’Ambari, dans le volet gauche, cliquez sur **Spark**.
2. Dans la page suivante, cliquez sur **Spark Thrift Servers (Serveur Thrift Spark)**.

    ![Redémarrer le serveur Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-1.png)
3. Vous devez voir les deux nœuds principaux sur lesquels le serveur Thrift Spark s’exécute. Cliquez sur l’un des nœuds principaux.

    ![Redémarrer le serveur Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-2.png)
4. La page suivante répertorie tous les services exécutés sur ce nœud principal. Dans la liste, cliquez sur le bouton de la liste déroulante en regard de Serveur Thrift Spark, puis cliquez sur **Stop (Arrêter)**.

    ![Redémarrer le serveur Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-3.png)
5. Répétez également ces étapes sur l’autre nœud principal.

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>Mes blocs-notes Jupyter ne s’exécutent pas comme prévu. Comment redémarrer le service ?
1. Lancez l’interface utilisateur web Ambari comme indiqué ci-dessus. Dans le volet de navigation gauche, cliquez sur **Jupyter**, sur **Actions de service**, puis sur **Redémarrer tout**. Cela permet au service Jupyter de démarrer sur tous les nœuds principaux.

    ![Redémarrer Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Redémarrer Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resource"></a>Comment savoir si mes ressources sont épuisées ?
1. Lancez l’interface utilisateur web Yarn comme indiqué ci-dessus. Dans la table des mesures de Cluster située en haut de l’écran, vérifiez les valeurs des colonnes **Mémoire utilisée** et **Mémoire totale**. Si les 2 valeurs sont très proches, le nombre de ressources ne sera peut-être pas suffisant pour démarrer l’application suivante. Cela s’applique également aux colonnes **VCores utilisés** et **Total des VCores**. En outre, dans la vue principale, si une application est restée dans l’état **ACCEPTÉ** et n’est pas passée à l’état **EN COURS D’EXÉCUTION** ou **Échec**. Cela peut également indiquer que le nombre de ressources est insuffisant pour démarrer.

    ![Limite des ressources](./media/hdinsight-apache-spark-resource-manager/resource-limit.png "Limite des ressources")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>Comment arrêter une application en cours d’exécution afin de libérer des ressources ?
1. Dans l’interface utilisateur Yarn, dans le volet gauche, cliquez sur **En cours d’exécution**. Dans la liste des applications en cours d’exécution, déterminez l’application à arrêter, puis cliquez sur l’**ID**.

    ![Arrêter App1](./media/hdinsight-apache-spark-resource-manager/kill-app1.png "Arrêter App1")

2. Cliquez sur **Arrêter l’application** dans le coin supérieur droit, puis cliquez sur **OK**.

    ![Arrêter App2](./media/hdinsight-apache-spark-resource-manager/kill-app2.png "Arrêter App2")

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=Jan17_HO4-->


