---
title: "Kit de ressources Azure pour IntelliJ - Déboguer des applications à distance sur Spark HDInsight | Documents Microsoft"
description: "Découvrez comment utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer à distance des applications exécutées sur des clusters Spark HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: f8368fc49547db8f6b200f26276cfb702cdf6159
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017


---
# <a name="use-azure-toolkit-for-intellij-to-debug-applications-remotely-on-hdinsight-spark"></a>Utiliser le Kit de ressources Azure pour IntelliJ afin de déboguer des applications à distance sur Spark HDInsight
Cet article fournit des instructions pas à pas sur l’utilisation d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de soumettre un travail Spark sur un cluster Spark HDInsight et effectuer un débogage à distance à partir de votre ordinateur de bureau. Pour ce faire, vous devez effectuer les étapes suivantes :

1. Créer un réseau virtuel Azure de site à site ou de point à site. Les étapes décrites dans ce document supposent d’utiliser un réseau de site à site.
2. Créer dans Azure HDInsight un cluster Spark faisant partie du réseau virtuel Azure de site à site.
3. Vérifier la connectivité entre votre ordinateur de bureau et le nœud principal du cluster.
4. Créer une application Scala dans IntelliJ IDEA et la configurer pour le débogage à distance.
5. Exécuter et déboguer l’application.

## <a name="prerequisites"></a>Composants requis
* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. Cet article utilise la version 15.0.1. Vous pouvez l’installer [ici](https://www.jetbrains.com/idea/download/).
* HDInsight Tools dans le kit de ressources Azure pour IntelliJ. HDInsight Tools pour IntelliJ est disponible dans le cadre du kit de ressources Azure pour IntelliJ. Pour obtenir des instructions sur l’installation du kit de ressources Azure, voir [Installation du kit de ressources Azure pour IntelliJ](../azure-toolkit-for-intellij-installation.md).
* Connectez-vous à votre abonnement Azure à partir d’IntelliJ IDEA. Suivez les instructions disponibles [ici](hdinsight-apache-spark-intellij-tool-plugin.md).
* Lorsque vous exécutez l’application Spark Scala sur un ordinateur Windows pour un débogage à distance, vous pouvez obtenir une exception liée à l’absence d’un fichier WinUtils.exe sur Windows, comme expliqué dans le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) . Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable à partir d’ici](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**. Vous devez ensuite ajouter une variable d’environnement **HADOOP_HOME** et définir la valeur de la variable sur **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Étape 1 : créer un réseau virtuel Azure
Suivez les instructions contenues dans les liens ci-dessous pour créer un réseau virtuel Azure, puis vérifiez la connectivité entre votre ordinateur de bureau et le réseau virtuel Azure.

* [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurer une connexion point à site à un réseau virtuel à l’aide de PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Étape 2 : créer un cluster Spark HDInsight
Vous devez également créer dans Azure HDInsight un cluster Apache Spark faisant partie du réseau virtuel Azure que vous avez créé. Utilisez les informations contenues dans l’article [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Dans le cadre de la configuration facultative, sélectionnez le réseau virtuel Azure que vous avez créé à l’étape précédente.

## <a name="step-3-verify-the-connectivity-between-the-cluster-headnode-and-your-desktop"></a>Étape 3 : vérifier la connectivité entre votre ordinateur de bureau et le nœud principal du cluster
1. Récupérez l’adresse IP du nœud principal. Ouvrez l’interface utilisateur Ambari du cluster. Dans le panneau du cluster, cliquez sur **Tableau de bord**.

    ![Rechercher l’adresse IP du nœud principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. À partir de l’interface utilisateur Ambari, cliquez sur **Hôtes**en haut à droite.

    ![Rechercher l’adresse IP du nœud principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Vous devez obtenir une liste de nœuds principaux, de nœuds worker et de nœuds zookeeper. Les nœuds principaux sont repérés à l’aide du préfixe **hn***. Cliquez sur le premier nœud principal.

    ![Rechercher l’adresse IP du nœud principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. En bas de la page qui s’ouvre, dans la zone **Résumé** , copiez l’adresse IP du nœud principal et le nom d’hôte.

    ![Rechercher l’adresse IP du nœud principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Ajoutez l’adresse IP et le nom d’hôte du nœud principal au fichier **hosts** de l’ordinateur sur lequel vous souhaitez exécuter et déboguer à distance les travaux Spark. Cela vous permettra de communiquer avec le nœud principal à l’aide de l’adresse IP et du nom d’hôte.

   1. Ouvrez un bloc-notes avec des autorisations élevées. Dans le menu Fichier, cliquez sur **Ouvrir** et accédez à l’emplacement du fichier hosts. Sur un ordinateur Windows, ce fichier se trouve dans le répertoire `C:\Windows\System32\Drivers\etc\hosts`.
   2. Ajoutez le code suivant au fichier **hosts** .

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. À partir de l’ordinateur que vous avez connecté au réseau virtuel Azure utilisé par le cluster HDInsight, vérifiez que vous pouvez exécuter une commande ping sur les nœuds principaux aussi bien avec l’adresse IP qu’avec le nom d’hôte.
7. Exécutez une commande SSH dans le nœud principal du cluster en suivant les instructions fournies dans la section [Connexion à un cluster HDInsight sous Linux](hdinsight-hadoop-linux-use-ssh-unix.md). À partir du nœud principal du cluster, exécutez une commande ping sur l’adresse IP de l’ordinateur de bureau. Vous devez tester la connectivité aux deux adresses IP affectées à l’ordinateur, une pour la connexion réseau et l’autre pour le réseau virtuel Azure auquel l’ordinateur est connecté.
8. Répétez également ces étapes pour l’autre nœud principal.

## <a name="step-4-create-a-spark-scala-application-using-the-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Étape 4 : créer une application Spark Scala à l’aide d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ et la configurer pour le débogage à distance
1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.

    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

   * Dans le volet gauche, sélectionnez **HDInsight**.
   * Dans le volet droit, sélectionnez **Spark on HDInsight (Scala)**(Spark on HDInsight (Scala)).
   * Cliquez sur **Suivant**.
2. Dans la fenêtre suivante, fournissez les informations de projet.

   * Fournissez un nom de projet et un emplacement de projet.
   * Pour **Project SDK**(Kit de développement logiciel (SDK) de projet), prenez soin d’indiquer une version de Java supérieure à la version 7.
   * Pour **Scala SDK** (Kit de développement logiciel de Scala (SDK)), cliquez sur **Créer**, cliquez sur **Télécharger**, puis sélectionnez la version de Scala à utiliser. **Assurez-vous que vous n’utilisez pas la version 2.11.x**. Cet exemple utilise la version **2.10.6**.

       ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-version.png)
   * Pour le **Kit de développement logiciel (SDK) Spark**, téléchargez et utilisez le Kit de développement logiciel (SDK) disponible [ici](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Vous pouvez également ignorer ce dernier et utiliser le [référentiel Spark Maven](http://mvnrepository.com/search?q=spark) à la place. Toutefois, vérifiez que vous disposez du référentiel Maven approprié pour développer vos applications Spark. (Par exemple, vous devez vous assurer que le module Spark Streaming est installé si vous utilisez la fonctionnalité de diffusion en continu de Spark. En outre, vérifiez que vous utilisez le référentiel libellé Scala 2.10 ; n’utilisez pas le référentiel Scala 2.11.)

       ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
   * Cliquez sur **Terminer**.
3. Le projet Spark crée automatiquement un artefact à votre intention. Pour voir l’artefact, procédez comme suit.

   1. Dans le menu **Fichier**, cliquez sur **Structure de projet**.
   2. Dans la boîte de dialogue **Structure de projet**, cliquez sur **Artefacts** pour afficher l’artefact créé par défaut.

       ![Créer un fichier jar](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)

      Vous pouvez également créer votre propre artefact en cliquant sur l’icône **+** mis en surbrillance dans la capture d’écran ci-dessus.
4. Dans la boîte de dialogue **Structure de projet**, cliquez sur **Projet**. Si le **SDK du projet** est défini sur 1.8, assurez-vous que le **niveau de langage du projet** est défini sur **7 - Diamonds, ARM, multi-catch, etc**.

    ![Définir le niveau de langage du projet](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/set-project-language-level.png)
5. Ajoutez des bibliothèques à votre projet. Pour ajouter une bibliothèque, cliquez avec le bouton droit sur le nom du projet dans l’arborescence du projet, puis cliquez sur **Open Module Settings**(Ouvrir les paramètres du module). Dans la boîte de dialogue **Structure de projet**, dans le volet gauche, cliquez sur **Bibliothèques**, cliquez sur le signe plus (+), puis cliquez sur **From Maven** (De Maven).

    ![Ajouter une bibliothèque](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    Dans la boîte de dialogue **Download Library from Maven Repository** (Télécharger la bibliothèque à partir du référentiel Maven), recherchez et ajoutez les bibliothèques suivantes.

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
6. Copiez `yarn-site.xml` et `core-site.xml` à partir du nœud principal du cluster et ajoutez-les au projet. Exécutez les commandes suivantes pour copier les fichiers. Vous pouvez utiliser [Cygwin](https://cygwin.com/install.html) pour exécuter les commandes `scp` suivantes afin de copier les fichiers à partir des nœuds principaux du cluster.

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Étant donné que nous avons déjà ajouté l’adresse IP et les noms d’hôtes des nœuds principaux du cluster au fichier hosts de notre ordinateur de bureau, nous pouvons utiliser les commandes **scp** de la manière suivante.

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Ajoutez ces fichiers à votre projet en les copiant dans le dossier **/src** dans l’arborescence de votre projet, par exemple `<your project directory>\src`.
7. Mettez à jour le fichier `core-site.xml` pour effectuer les modifications suivantes.

   1. `core-site.xml` inclut la clé chiffrée du compte de stockage associé au cluster. Dans le fichier `core-site.xml` que vous avez ajouté au projet, remplacez la clé chiffrée par la clé de stockage réelle associée au compte de stockage par défaut. Voir [Gérer vos clés d’accès de stockage](../storage/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   2. Supprimez les entrées suivantes du fichier `core-site.xml`.

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   3. Enregistrez le fichier .
8. Ajoutez la classe principale pour votre application. Dans l’**Explorateur de projets**, cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis cliquez sur **Scala class** (Classe Scala).

    ![Ajouter le code source](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
9. Dans la boîte de dialogue **Create New Scala Class** (Créer une classe Scala), indiquez un nom, dans la zone **Kind** (Genre), sélectionnez **Object** (Objet), puis cliquez sur **OK**.

    ![Ajouter le code source](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
10. Collez le code suivant dans le fichier `MyClusterAppMain.scala` . Ce code crée le contexte Spark et lance une méthode `executeJob` à partir de l’objet `SparkSample`.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasbs:///HVACOut")
          }
        }

1. Répétez les étapes 8 et 9 ci-dessus pour ajouter un nouvel objet Scala appelé `SparkSample`. Ajoutez le code suivant à cette classe. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la septième colonne du fichier CSV et écrit la sortie dans **/HVACOut** sous le conteneur de stockage par défaut du cluster.

       import org.apache.spark.SparkContext

       object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }

       }
2. Répétez les étapes 8 et 9 ci-dessus pour ajouter une nouvelle classe appelée `RemoteClusterDebugging`. Cette classe implémente l’infrastructure de test Spark utilisée pour le débogage des applications. Ajoutez le code suivant à la classe `RemoteClusterDebugging` .

       import org.apache.spark.{SparkConf, SparkContext}
       import org.scalatest.FunSuite

       class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\WORK\IntelliJApps\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasbs:///HVACOut")
         }
       }

   Deux points importants sont ici à prendre en considération :

   * Pour `.set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, assurez-vous que le fichier JAR de l’assembly Spark est disponible sur le stockage de cluster dans le chemin d’accès spécifié.
   * Pour `setJars`, spécifiez l’emplacement où le fichier jar de l’artefact sera créé. En général, il s’agit du répertoire `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
3. Dans la classe `RemoteClusterDebugging`, cliquez avec le bouton droit sur le mot-clé `test` et sélectionnez **Create RemoteClusterDebugging Configuration** (Créer une configuration RemoteClusterDebugging).

   ![Créer une configuration distante](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)
4. Dans la boîte de dialogue, entrez un nom pour la configuration, puis sélectionnez **Test kind** (Type de test) comme **Test name** (Nom de test). Laissez toutes les autres valeurs par défaut, cliquez sur **Appliquer**, puis cliquez sur **OK**.

   ![Créer une configuration distante](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
5. Une liste déroulante **Remote Run** (Exécution à distance) s’affiche maintenant dans la barre de menus.

   ![Créer une configuration distante](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Étape 5 : exécuter l’application en mode débogage
1. Dans votre projet IntelliJ IDEA, ouvrez `SparkSample.scala` et créez un point d’arrêt en regard de « val rdd1 ». Dans le menu contextuel permettant de créer un point d’arrêt, sélectionnez **line in function executeJob**(ligne dans la fonction executeJob).

    ![Ajouter un point d’arrêt](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Cliquez sur le bouton **Debug Run** (Exécuter le débogage) situé en regard de la liste déroulante de configuration **Remote Run** (Exécution à distance) pour commencer à exécuter l’application.

    ![Exécuter le programme en mode débogage](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Lorsque l’exécution du programme atteint le point d’arrêt, un onglet **Debugger** (Débogueur) doit apparaître dans le volet inférieur.

    ![Exécuter le programme en mode débogage](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Cliquez sur l’icône (**+**) pour ajouter une surveillance, comme l’illustre l’image ci-dessous.

    ![Exécuter le programme en mode débogage](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Dans ce cas, étant donné que l’application s’est arrêtée avant la création de la variable `rdd1`, cette surveillance nous permet de voir les cinq premières lignes de la variable `rdd`. Appuyez sur **ENTRÉE**.

    ![Exécuter le programme en mode débogage](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Dans l’image ci-dessus, on constate que, au moment de l’exécution, il est possible d’interroger des téraoctets de données et de corriger la progression de votre application. Par exemple, dans la sortie illustrée ci-dessus, vous pouvez voir que la première ligne de la sortie est un en-tête. Sur cette base, vous pouvez modifier votre code d’application pour ignorer la ligne d’en-tête si nécessaire.
5. Vous pouvez maintenant cliquer sur l’icône **Resume Program** (Reprendre le programme) pour poursuivre l’exécution de votre application.

    ![Exécuter le programme en mode débogage](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Si l’application s’exécute correctement, vous devez obtenir une sortie similaire à ce qui suit.

    ![Exécuter le programme en mode débogage](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Voir aussi
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
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

