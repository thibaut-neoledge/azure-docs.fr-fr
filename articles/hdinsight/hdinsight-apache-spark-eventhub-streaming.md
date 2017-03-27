---
title: "Diffuser des données en continu à partir de Event Hubs avec Apache Spark dans Azure HDInsight | Microsoft Docs"
description: "Instructions détaillées sur l’envoi d’un flux de données à un Event Hub Azure et la réception de ces événements dans Spark à l’aide d’une application Scala"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: ef0757914828128ed4edf569aeb3716300b17dee
ms.lasthandoff: 01/24/2017


---
# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight"></a>Diffusion en continu (streaming) de Spark : traitement d’événements d’Azure Event Hubs avec un cluster Apache Spark sous HDInsight
La diffusion en continu de Spark développe l’API Spark de base pour générer des applications de traitement de flux de données évolutives, à haut débit et à tolérance de pannes. Les données peuvent provenir de nombreuses sources. Dans cet article, nous utilisons Azure Event Hubs pour recevoir des données. Event Hubs est un système de réception hautement évolutif pouvant recevoir des millions d’événements par seconde. 

Ce didacticiel explique comme créer un Event Hub Azure, recevoir des messages dans un Event Hub à l’aide d’une application console écrite en Java, et à les récupérer en parallèle à l’aide d’une application Spark écrite en Scala. Cette application utilise les données diffusées en continu via les Event Hubs, et les achemine vers différentes sorties (objet blob Azure Storage, table Hive et table SQL).

> [!NOTE]
> Pour suivre les instructions de cet article, vous devez utiliser les deux versions du portail Azure. Pour créer un Event Hub, vous utiliserez le [Portail Azure Classic](https://manage.windowsazure.com). Pour travailler avec le cluster HDInsight Spark, vous utiliserez le [Portail Azure](https://portal.azure.com/).  
> 
> 

**Configuration requise :**

Vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir d’ [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE Java. Cet article utilise IntelliJ IDEA 15.0.1. Vous pouvez l’installer à partir d’ [ici](https://www.jetbrains.com/idea/download/).
* Microsoft JDBC driver pour SQL Server, v4.1 ou version ultérieure. Ceci est nécessaire pour écrire les données d’événement dans une base de données SQL Server. Vous pouvez l’installer à partir d’ [ici](https://msdn.microsoft.com/sqlserver/aa937724.aspx).
* base de données SQL Azure. Pour obtenir des instructions, consultez [Créer une base de données SQL en quelques minutes](../sql-database/sql-database-get-started.md).

## <a name="what-does-this-solution-do"></a>Que fait cette solution ?
Voici le déroulement de la solution de diffusion en continu :

1. Créez un Event Hub Azure qui doit recevoir un flux d’événements.
2. Exécutez une application autonome locale qui génère des événements et les envoie à l’Event Hub Azure. L’exemple d’application qui effectue cette opération est publié à l’adresse [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).
3. Exécutez une application de diffusion en continu à distance sur un cluster Spark qui lit les événements de diffusion en continu à partir de l’Event Hub Azure, et les envoie à différents emplacements (objet blob Azure, table Hive et table Base de données SQL). 

## <a name="create-azure-event-hub"></a>Créer un Event Hub Azure
1. Dans le [portail Azure](https://manage.windowsazure.com), sélectionnez **NOUVEAU** > **Service Bus** > **Hub d’événements** > **Création personnalisée**.
2. Sur l’écran **Ajouter un nouvel hub d’événements**, entrez un **nom de hub d’événements**, sélectionnez la **région** dans laquelle créer le hub, puis créez un espace de noms ou sélectionnez-en un existant. Cliquez sur la **flèche** pour continuer.
   
    ![page 1 de l’assistant](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "Créer un hub d’événements Azure")
   
   > [!NOTE]
   > Vous devez sélectionner le même **emplacement** que celui de votre cluster Apache Spark dans HDInsight pour réduire la latence et les coûts.
   > 
   > 
3. Sur l’écran **Configurer un hub d’événements**, entrez le **nombre de partitions** et les valeurs de **rétention des messages**, puis cliquez sur la coche. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages. Notez le nombre de partitions, car vous en aurez besoin ultérieurement.
   
    ![page 2de l’assistant](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Spécifier la taille des partitions et les jours de rétention du hub d’événements")
4. Cliquez sur le hub d’événements que vous avez créé, cliquez sur **Configurer**, puis créez deux stratégies d’accès pour le hub d’événements.
   
    <table>
    <tr><th>Nom</th><th>Autorisations</th></tr>
    <tr><td>mysendpolicy</td><td>Envoyer</td></tr>
    <tr><td>myreceivepolicy</td><td>Écouter</td></tr>
    </table>
   
    Après avoir créé les autorisations, sélectionnez l’icône **Enregistrer** située en bas de page. Ceci crée des stratégies d’accès partagé qui seront utilisées pour envoyer vers (**mysendpolicy**) et écouter (**myreceivepolicy**) ce hub d’événements.
   
    ![stratégies](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Créer des stratégies de hub d’événements")
5. Sur la même page, notez les clés de stratégie générées pour les deux stratégies. Enregistrez ces clés, car elles seront utilisées plus tard.
   
    ![clés de stratégie](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Enregistrer les clés de stratégie")
6. Sur la page **Tableau de bord**, cliquez sur **Informations de connexion** en bas pour récupérer et enregistrer les chaînes de connexion pour le hub d’événements utilisant les deux stratégies.
   
    ![clés de stratégie](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Enregistrer les chaînes de connexion de stratégie")

## <a name="use-a-scala-application-to-send-messages-to-event-hub"></a>Utiliser une application Scala pour envoyer des messages à l’Event Hub
Dans cette section, vous utilisez une application Scala autonome locale pour envoyer un flux d’événements à l’Event Hub Azure que vous avez créé à l’étape précédente. Cette application est disponible sur GitHub à l’adresse [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Les étapes décrites ici supposent que vous avez déjà dupliqué ce dépôt GitHub.

1. Ouvrez l’application **EventhubsSampleEventProducer**dans IntelliJ IDEA.
2. Créez le projet. Dans le menu **Build** (Générer), cliquez sur **Make Project** (Créer le projet). Le fichier JAR de sortie est créé sous **\out\artifacts**.

> [!TIP]
> Vous pouvez également utiliser une option disponible dans IntelliJ IDEA pour créer directement le projet à partir d’un dépôt GitHub. Pour comprendre comment utiliser cette approche, suivez les instructions dans la section suivante. Notez que de nombreuses étapes décrites dans la section suivante ne sont pas applicables pour l’application Scala que vous créez à cette étape. Par exemple :
> 
> * Vous ne devrez pas mettre à jour le fichier POM pour inclure la version de Spark. C’est parce qu’il n’y a aucune dépendance de Spark pour créer cette application.
> * Vous ne devrez pas ajouter de fichiers jar de dépendance à la bibliothèque de projet. C’est parce que ces fichiers ne sont pas requis pour ce projet.
> 
> 

## <a name="update-the-scala-streaming-application-for-receiving-the-events"></a>Mettre à jour l’application de diffusion en continu de Scala pour recevoir les événements
Un exemple d’application Scala pour recevoir l’événement et le router vers différentes destinations est disponible à l’adresse [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Suivez les étapes ci-dessous pour mettre à jour l’application et de créer le fichier jar de sortie.

1. Lancez IntelliJ IDEA, sélectionnez **Check out from Version Control** (Extraire du contrôle de version) dans l’écran de démarrage, puis cliquez sur **Git**.
   
    ![Obtenir des sources de Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)
2. Dans la boîte de dialogue **Clone Repository** (Cloner le dépôt), entrez l’URL du dépôt Git à partir duquel opérer le clonage, spécifiez le répertoire vers lequel cloner, puis cliquez sur **Clone**. (Cloner).
   
    ![Cloner à partir de Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)
3. Suivez les invites jusqu’à ce que le projet soit entièrement cloné. Appuyez sur **Alt + 1** pour ouvrir la **Vue du projet**. Elle doit ressembler à ceci :
   
    ![Vue du projet](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
4. Assurez-vous que le code d’application est compilé avec Java8. Pour ce faire, cliquez sur **Fichier**, sur **Structure de projet**, puis sur l’onglet **Projet**, assurez-vous que le niveau de langue du projet est défini sur **8 - Lambdas, type annotations, etc.** (8- Expressions lambda, annotations de type, etc).
   
    ![Structure de projet](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)
5. Ouvrez le fichier **pom.xml** et assurez-vous que la version de Spark est correcte. Dans le nœud  <properties> , recherchez l’extrait de code suivant et vérifiez la version de Spark.
   
        <scala.version>2.10.4</scala.version>
        <scala.compat.version>2.10.4</scala.compat.version>
        <scala.binary.version>2.10</scala.binary.version>
        <spark.version>1.6.2</spark.version>
6. L’application requiert deux fichiers jar de dépendance :
   
   * **Fichier jar du récepteur EventHub**. Ce fichier est nécessaire pour permettre à Spark de recevoir les messages de l’Event Hub. Pour utiliser ce fichier .jar, mettez à jour le fichier **pom.xml** afin d’ajouter le code suivant sous `<dependencies>`.
     
           <dependency>
             <groupId>com.microsoft.azure</groupId>
             <artifactId>spark-streaming-eventhubs_2.10</artifactId>
             <version>1.6.0</version>
           </dependency> 
   * **fichier jar du pilote JDBC**. Ce fichier est nécessaire pour écrire les messages reçus de l’Event Hub dans une base de données SQL Azure. Vous pouvez télécharger la version&4;.1 ou une version ultérieure de ce fichier jar [ici](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Ajoutez la référence à ce fichier jar dans la bibliothèque de projet. Procédez comme suit :
     
     1. Dans la fenêtre IntelliJ IDEA où l’application est ouverte, cliquez sur **File** (Fichier), sur **Project Structure** (Structure de projet), puis sur **Libraries** (Bibliothèques). 
     2. Cliquez sur l’icône Ajouter (![icône Ajouter](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), sur **Java**, puis accédez à l’emplacement où vous avez téléchargé le fichier jar du pilote JDBC. Suivez les invites pour ajouter le fichier jar à la bibliothèque de projet.
        
         ![ajouter les dépendances manquantes](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Ajouter les fichiers jars de dépendance manquants")
     3. Cliquez sur **Apply**.
7. Créez le fichier jar de sortie. Procédez comme suit.
   
   1. Dans la boîte de dialogue **Project Structure** (Structure de projet) cliquez sur **Artifacts** (Artefacts), puis sur le signe plus. Dans la boîte de dialogue contextuelle, cliquez sur **JAR**, puis sur **À partir de modules ayant des dépendances**.
      
       ![Créer un fichier jar](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)
   2. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, cliquez sur le bouton de sélection (![ellipse](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) en regard de **Classe principale**.
   3. Dans la boîte de dialogue **Select Main Class** (Sélectionner une classe principale), sélectionnez l’une des classes disponibles, puis cliquez sur **OK** (OK).
      
       ![Créer un fichier jar](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)
   4. Dans la boîte de dialogue **Create JAR from Modules** (Créer un fichier JAR à partir de modules), assurez-vous que l’option **Extract to the target JAR** (Extraire vers le fichier JAR cible) est activée, puis cliquez sur **OK** (OK). Cela crée un fichier JAR contenant toutes les dépendances.
      
       ![Créer un fichier jar](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)
   5. L’onglet **Output Layout** (Disposition de la sortie) répertorie tous les fichiers jar inclus dans le cadre du projet Maven. Vous pouvez sélectionner et supprimer ceux sur lesquels l’application Scala n’a aucune dépendance directe. Pour l’application que nous créons ici, vous pouvez les supprimer tous sauf le dernier (**microsoft-spark-streaming-examples compile output**). Sélectionnez les fichiers JAR à supprimer, puis cliquez sur l’icône **Delete** (Supprimer) (![icône de suppression](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Créer un fichier jar](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)
      
       Assurez-vous que la case à cocher **Générer à la création** est activée, ce qui garantit la création du fichier JAR à chaque génération ou mise à jour du projet. Cliquez sur **Apply** (Appliquer), puis sur **OK** (OK).
   6. Sous l’onglet **Output Layout** (Disposition de la sortie), en bas à droite de la zone **Available Elements (Éléments disponibles)**, vous pouvez voir le fichier jar SQL JDBC jar que vous avez ajouté précédemment à la bibliothèque de projet. Vous devez l’ajouter à l’onglet **Output Layout** (Disposition de la sortie). Cliquez avec le bouton droit sur le fichier jar, puis cliquez sur **Extract Into Output Root**(Extraire dans la racine de sortie).
      
       ![Extraire un fichier jar de dépendance](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  
      
       L’onglet **Output Layout** (Disposition de la sortie) doit maintenant ressembler à ceci.
      
       ![Onglet Sortie finale](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)        
      
       Dans la boîte de dialogue **Project Structure** (Structure de projet), cliquez sur **Apply** (Appliquer), puis sur **OK** (OK).    
   7. Dans la barre de menus, cliquez sur **Build** (Générer), puis sur **Make Project** (Créer le projet). Vous pouvez également cliquer sur **Générer les artefacts** pour créer le fichier JAR. Le fichier JAR de sortie est créé sous **\out\artifacts**.
      
       ![Créer un fichier jar](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Exécuter les applications à distance sur un cluster Spark à l’aide de Livy
Nous allons utiliser Livy pour exécuter l’application de diffusion en continu à distance sur un cluster Spark. Pour une présentation détaillée de l’utilisation de Livy avec le cluster HDInsight Spark, voir [Soumettre des travaux à distance à un cluster Apache Spark sur Azure HDInsight](hdinsight-apache-spark-livy-rest-interface.md). Avant de commencer à exécuter les travaux à distance pour diffuser des événements à l’aide de Spark, vous devez effectuer quelques opérations :

1. Démarrez l’application autonome locale pour générer des événements et les envoyer à l’Event Hub. Pour ce faire, utilisez la commande suivante :
   
        java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1
2. Copiez le fichier jar de diffusion en continu (**microsoft-spark-streaming-examples.jar**) vers le stockage d’objets blob Azure associé au cluster. Le fichier jar est ainsi accessible à Livy. Pour ce faire, vous pouvez utiliser l’utilitaire de ligne de commande [**AzCopy**](../storage/storage-use-azcopy.md). De nombreux autres clients permettent également de télécharger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](hdinsight-upload-data.md).
3. Installez CURL sur l’ordinateur à partir duquel vous exécutez ces applications. Nous utilisons CURL pour appeler les points de terminaison Livy afin d’exécuter les travaux à distance.

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Exécuter les applications pour recevoir les événements dans un objet blob Azure Storage en tant que texte
Ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante (en remplaçant le nom/mot de passe d’utilisateur et le nom du cluster) :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputBlob.txt** sont définis comme suit :

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Nous devons comprendre quels sont les paramètres dans le fichier d’entrée :

* **file** est le chemin d’accès au fichier jar d’application sur le compte Azure Storage associé au cluster.
* **className** est le nom de la classe dans le fichier jar.
* **args** est la liste des arguments requis par la classe.
* **numExecutors** est le nombre de cœurs que Spark utilise pour exécuter l’application de diffusion en continu. Il doit toujours y avoir au moins deux fois le nombre de partitions de l’Event Hub.
* **executorMemory**, **executorCores** et **driverMemory** sont des paramètres utilisés pour affecter les ressources nécessaires à l’application de diffusion en continu.

> [!NOTE]
> Il est inutile de créer les dossiers de sortie (EventCheckpoint, EventCount/EventCount10) utilisés comme paramètres. L’application de diffusion en continu les crée pour vous.
> 
> 

Lorsque vous exécutez la commande, vous devez voir une sortie similaire à ce qui suit :

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Prenez note de l’ID de lot dans la dernière ligne de la sortie (dans cet exemple, «&1; »). Pour vérifier que l’application s’exécute correctement, vous pouvez consulter votre compte de stockage Azure associé au cluster, et devriez y voir le dossier **/nombre d’événements/EventCount10** créé. Ce dossier doit contenir les objets blob qui capturent le nombre d’événements traités pendant la période spécifiée pour le paramètre **batch-interval-in-seconds**.

L’application continue à s’exécuter jusqu’à ce que vous l’arrêtiez. Pour ce faire, utilisez la commande suivante :

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Exécuter les applications pour recevoir les événements dans un objet blob Azure Storage en tant que fichier JSON
Ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante (en remplaçant le nom/mot de passe d’utilisateur et le nom du cluster) :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputJSON.txt** sont définis comme suit :

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Les paramètres sont similaires à ce que vous avez spécifié pour la sortie de texte à l’étape précédente. Une fois encore, il est inutile de créer les dossiers de sortie (EventCheckpoint, EventCount/EventCount10) utilisés comme paramètres. L’application de diffusion en continu les crée pour vous.

 Après avoir exécuté la commande, vous pouvez consulter votre compte de stockage Azure associé au cluster, et devriez y voir le dossier **/EventStore10** créé. Ouvrez n’importe quel fichier ayant le préfixe **part-**. Vous devriez y voir les événements traités au format JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Exécuter les applications pour recevoir les événements dans une table Hive
Pour exécuter l’application qui diffuse des événements dans une table Hive, vous avez besoin de composants supplémentaires. Ces composants sont les suivants :

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-SGBDR-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Les fichiers **.jar** sont disponibles sur votre cluster HDInsight Spark à l’adresse `/usr/hdp/current/spark-client/lib`. Le fichier **hive-site.xml** est disponible à l’adresse `/usr/hdp/current/spark-client/conf`.

Vous pouvez utiliser [WinScp](http://winscp.net/eng/download.php) pour copier ces fichiers à partir du cluster sur votre ordinateur local. Vous pouvez ensuite vous servir d’outils pour copier ces fichiers sur votre compte de stockage associé au cluster. Pour plus d’informations sur la façon de charger des fichiers sur le compte de stockage, voir [Téléchargement de données pour des tâches Hadoop dans HDInsight](hdinsight-upload-data.md).

Après avoir copié les fichiers sur votre compte de stockage Azure, ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante (en remplaçant le nom/mot de passe d’utilisateur et le nom du cluster) :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputHive.txt** sont définis comme suit :

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Les paramètres sont similaires à ce que vous avez spécifié pour la sortie de texte aux étapes précédentes. Une fois encore, il est inutile de créer les dossiers de sortie(EventCheckpoint, EventCount/EventCount10) ou la table Hive de sortie (EventHiveTable10) utilisés comme paramètres. L’application de diffusion en continu les crée pour vous. Notez que l’option **jars** et **files** inclut des chemins d’accès aux fichiers .jar et au fichier hive-site.xml que vous avez copiés vers le compte de stockage.

Pour vérifier que la table Hive a été correctement créée, vous pouvez exécuter SSH dans le cluster et exécuter des requêtes Hive. Pour obtenir des instructions, consultez [Utilisation de Hive avec Hadoop dans HDInsight avec SSH](hdinsight-hadoop-use-hive-ssh.md). Après vous être connecté à l’aide de SSH, vous pouvez exécuter la commande suivante pour vérifier que la table Hive, **EventHiveTable10**, est créée.

    show tables;

Le résultat doit ressembler à ce qui suit :

    OK
    eventhivetable10
    hivesampletable

Vous pouvez également exécuter une requête SELECT pour afficher le contenu de la table.

    SELECT * FROM eventhivetable10 LIMIT 10;

Un résultat similaire à ce qui suit s’affiche normalement :

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Exécuter les applications pour recevoir les événements dans une table de Base de données SQL Azure
Avant d’exécuter cette étape, assurez-vous que vous disposez d’une base de données SQL Azure. Vous allez avoir besoin des valeurs de nom de base de données, de nom de serveur de base de données, ainsi que des informations d’identification de l’administrateur de base de données en tant que paramètres. Il est cependant inutile de créer la table de base de données. L’application de diffusion en continu la crée pour vous.

Ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputSQL.txt** sont définis comme suit :

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Pour vérifier que l’application s’exécute correctement, vous pouvez vous connecter à la base de données SQL Azure à l’aide de SQL Server Management Studio. Pour obtenir des instructions sur la procédure à suivre, voir [Se connecter à Base de données SQL avec SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md). Une fois connecté à la base de données, vous pouvez accéder à la table **EventContent** créée par l’application de diffusion en continu. Vous pouvez exécuter une requête rapide pour obtenir les données de la table. Exécutez la requête suivante :

    SELECT * FROM EventCount

Le résultat ressemble à ce qui suit :

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
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
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

