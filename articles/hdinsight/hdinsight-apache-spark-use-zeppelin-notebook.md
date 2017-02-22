---
title: "Utiliser l’action de script pour installer les blocs-notes Zeppelin pour un cluster Spark sur Azure HDInsight | Microsoft Docs"
description: "Instructions détaillées sur l’installation et l’utilisation des blocs-notes Zeppelin avec les clusters Spark sur HDInsight Linux."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: eadf0611ca46a975c364a1b073828c6c3faf5f77


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>Installer les blocs-notes Zeppelin pour un cluster Apache Spark sur HDInsight

Découvrez comment installer des blocs-notes Zeppelin sur des clusters Apache Spark et comment utiliser les blocs-notes Zeppelin pour exécuter des tâches Spark.

> [!IMPORTANT]
> Si vous avez approvisionné des clusters Spark 1.6 sur HDInsight 3.5. Vous pouvez accéder aux blocs-notes Zeppelin par défaut en utilisant les instructions de la page [Utilisation de blocs-notes Zeppelin avec un cluster Apache Spark sur HDInsight Linux](hdinsight-apache-spark-zeppelin-notebook.md). Si vous souhaitez utiliser Zeppelin sur les versions de cluster HDInsight 3.3, 3.4, ou Spark 2.0 sur HDInsight 3.5, vous devez suivre les instructions de cet article pour installer Zeppelin.
>
>

**Configuration requise :**

* Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un client SSH. Pour les distributions Linux et Unix ou pour Macintosh OS X, la commande `ssh` est fournie avec le système d'exploitation. Pour Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

  > [!NOTE]
  > Si vous souhaitez utiliser un client SSH autre que `ssh` ou PuTTY, veuillez consulter la documentation de votre client qui explique comment établir un tunnel SSH.
  >
  >
* Un navigateur Web qui peut être configuré pour utiliser un proxy SOCKS
* **(facultatif)**: un plug-in comme [FoxyProxy](http://getfoxyproxy.org/,) pouvant appliquer des règles qui acheminent uniquement les demandes spécifiques via le tunnel.

  > [!WARNING]
  > Sans un plug-in comme FoxyProxy, toutes les demandes effectuées via le navigateur peuvent être routées via le tunnel. Cela peut entraîner un chargement plus lent des pages Web dans votre navigateur.
  >
  >

## <a name="install-zeppelin-on-a-spark-cluster"></a>Installation de Zeppelin sur un cluster Spark
Vous pouvez installer Zeppelin sur un cluster Spark à l’aide d’une action de script. L’action de script utilise des scripts personnalisés pour installer sur le cluster des composants qui ne sont pas disponibles par défaut. Vous pouvez utiliser le script personnalisé pour installer Zeppelin à partir du portail Azure, à l’aide du Kit de développement logiciel (SDK) .NET HDInsight ou à l’aide d’Azure PowerShell. Vous pouvez utiliser le script pour installer Zeppelin lors de la création du cluster ou lorsque le cluster est prêt à fonctionner. Les liens dans les sections ci-dessous fournissent les instructions sur la marche à suivre.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure
Pour connaître les instructions liées à l’utilisation du portail Azure pour exécuter une action de script afin d’installer Zeppelin, consultez [Personnalisation des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Vous devez apporter quelques modifications aux instructions de cet article.

* Vous devez utiliser le script pour installer Zeppelin. Le script personnalisé permettant d’installer Zeppelin sur un cluster Spark sur HDInsight est disponible aux liens suivants :

  * Pour les clusters Spark 1.6.0 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Pour les clusters Spark 1.5.2 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Vous devez exécuter l'action de script uniquement sur le nœud principal.
* Le script n'a pas besoin de paramètres.

### <a name="using-hdinsight-net-sdk"></a>Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Pour connaître les instructions liées à l’utilisation du Kit de développement logiciel (SDK) HDInsight .NET pour exécuter une action de script afin d’installer Zeppelin, consultez [Personnalisation des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Vous devez apporter quelques modifications aux instructions de cet article.

* Vous devez utiliser le script pour installer Zeppelin. Le script personnalisé permettant d’installer Zeppelin sur un cluster Spark sur HDInsight est disponible aux liens suivants :

  * Pour les clusters Spark 1.6.0 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Pour les clusters Spark 1.5.2 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Le script n'a pas besoin de paramètres.
* Définissez le type de cluster que vous créez sur Spark.

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Utilisez l'extrait de code PowerShell suivant pour créer un cluster Spark sur HDInsight Linux sur lequel Zeppelin est installé. Selon la version de cluster Spark dont vous disposez, vous devez mettre à jour l’extrait de code PowerShell ci-dessous pour inclure le lien vers le script personnalisé correspondant.

* Pour les clusters Spark 1.6.0 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Pour les clusters Spark 1.5.2 : `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Accès au bloc-notes Zeppelin

Une fois Zeppelin installé à l’aide d’une action de script, vous pouvez procéder comme suit pour accéder au bloc-notes Zeppelin sur le cluster Spark. Dans cette section, vous allez apprendre à exécuter des instructions %sql et %hive.

1. À partir du navigateur Web, ouvrez le système d’extrémité suivant :

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. Créer un nouveau bloc-notes. Dans le volet d'en-tête, cliquez sur **Bloc-notes**, puis sur **Créer une note**.

    ![Créer un nouveau bloc-notes Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Créer un nouveau bloc-notes Zeppelin")

    Dans la même page, sous le titre **Bloc-notes**, un nouveau bloc-notes dont le nom commence par **Note XXXXXXXXX** doit s’afficher. Cliquez sur le nouveau bloc-notes.
3. Sur la page web du nouveau bloc-notes, cliquez sur l'en-tête et modifiez le nom du bloc-notes si vous le souhaitez. Appuyez sur ENTRÉE pour enregistrer la modification de nom. Vérifiez également que l’en-tête du bloc-notes indique l’état **Connected** dans le coin supérieur droit.

    ![État du bloc-notes Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "État du bloc-notes Zeppelin")

### <a name="run-sql-statements"></a>Exécuter des instructions SQL
1. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\HdiSamples\SensorSampleData\hvac**.

    Collez l’extrait suivant dans le paragraphe vide créé par défaut dans le nouveau bloc-notes.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    Appuyez sur **MAJ + ENTRÉE** ou cliquez sur le bouton **Lire** pour que le paragraphe exécute l'extrait de code. L’état indiqué dans le coin supérieur droit du paragraphe doit progresser de READY, PENDING, RUNNING à FINISHED. Le résultat s’affiche au bas du même paragraphe. La capture d’écran ressemble à ceci :

    ![Créer une table temporaire à partir de données brutes](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Créer une table temporaire à partir de données brutes")

    Vous pouvez également indiquer un titre pour chaque paragraphe. Dans le coin droit, cliquez sur l’icône **Settings**, puis sur **Show title**.
2. Vous pouvez maintenant exécuter des instructions Spark SQL dans la table **hvac** . Collez la requête suivante dans un nouveau paragraphe. La requête récupère l’ID de bâtiment et la différence entre les températures cibles et réelles pour chaque bâtiment à une date donnée. Appuyez sur **MAJ + ENTRÉE**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    L'instruction **%sql** du début indique au bloc-notes d'utiliser l'interpréteur Spark SQL. Vous pouvez consulter les interpréteurs définis dans l'onglet **Interpreter** dans l'en-tête du bloc-notes.

    La capture d’écran qui suit présente le résultat.

    ![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")

     Cliquez sur les options d’affichage (mis en exergue dans un rectangle) pour basculer entre les différentes représentations du même résultat. Cliquez sur **Paramètres** pour choisir ce qui constitue la clé et les valeurs dans le résultat. La capture d'écran ci-dessus utilise la clé **buildingID** et la moyenne **temp_diff** comme valeur.
3. Vous pouvez également exécuter des instructions Spark SQL à l’aide de variables dans la requête. L’extrait suivant montre comment définir la variable **Temp**dans la requête avec les valeurs possibles d’interrogation. Lors de la première exécution de la requête, une liste déroulante est automatiquement renseignée avec les valeurs que vous avez spécifiées pour la variable.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    Collez cet extrait dans un nouveau paragraphe, puis appuyez sur **MAJ + ENTRÉE**. La capture d’écran qui suit présente le résultat.

    ![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")

    Pour les requêtes suivantes, vous pouvez sélectionner une nouvelle valeur dans la liste déroulante et réexécuter la requête. Cliquez sur **Paramètres** pour choisir ce qui constitue la clé et les valeurs dans le résultat. La capture d'écran ci-dessus utilise la clé **buildingID**, la moyenne **temp_diff** comme valeur, et le groupe **targettemp**.
4. Redémarrez l’interpréteur Spark SQL pour quitter l’application. Cliquez sur l’onglet **Interpreter** (Interpréteur) en haut de l’écran et, pour l’interpréteur Spark, cliquez sur **restart** (redémarrer).

    ![Redémarrer l’interpréteur Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Redémarrer l’interpréteur Zeppelin")

### <a name="run-hive-statements"></a>Exécutez des instructions hive
1. À partir du bloc-notes Zeppelin, cliquez sur le bouton **Interpreter** .

    ![Mettre à jour l’interpréteur Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Mettre à jour l’interpréteur Hive")
2. Pour l’interpréteur **hive**, cliquez sur **edit** (modifier).

    ![Mettre à jour l’interpréteur Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Mettre à jour l’interpréteur Hive")

    Mettez les propriétés suivantes à jour.

   * Définissez **default.password** sur le mot de passe que vous avez spécifié pour l’utilisateur administrateur lors de la création du cluster HDInsight Spark.
   * Définissez **default.url** sur `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Remplacez **\<spark_cluster_name>** par le nom de votre cluster Spark.
   * Définissez **default.user** sur le nom de l’utilisateur administrateur que vous avez spécifié lors de la création du cluster. Par exemple, *admin*.
3. Cliquez sur **Save** (Enregistrer) et lorsque vous êtes invité à redémarrer l’interpréteur hive, cliquez sur **OK**.
4. Créez un nouveau bloc-notes et exécutez l’instruction suivante pour répertorier toutes les tables hive sur le cluster.

        %hive
        SHOW TABLES

    Par défaut, un cluster HDInsight contient un exemple de table nommé **hivesampletable** , donc la sortie suivante doit s’afficher.

    ![Sortie Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Sortie Hive")
5. Exécutez l’instruction suivante pour répertorier les enregistrements dans la table.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    Un résultat similaire à ce qui suit doit s’afficher.

    ![Sortie Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Sortie Hive")

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
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
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


