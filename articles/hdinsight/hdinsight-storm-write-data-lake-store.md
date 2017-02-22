---
title: "Utilisation d’Azure Data Lake Store avec Apache Storm sur Azure HDInsight"
description: "Apprenez à écrire des données dans Azure Data Lake Store à partir d’une topologie Apache Storm sur HDInsight. Ce document et l’exemple associé illustrent la manière dont le composant HdfsBolt peut être utilisé pour écrire des données dans le Data Lake Store."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 0342c13e48d3f3605dcc169523d7d8d2d7aedba8


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight (Java)

Azure Data Lake Store est un service de stockage cloud compatible HDFS qui offre à vos données un haut niveau de débit, de disponibilité, de durabilité et de fiabilité. Dans ce document, vous allez apprendre à utiliser une topologie Storm basée sur Java pour écrire des données dans Azure Data Lake Store à l’aide du composant [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) fourni avec Apache Storm.

> [!IMPORTANT]
> L’exemple de topologie utilisé dans ce document repose sur les composants inclus avec Storm sur les clusters HDInsight. Il sera peut-être nécessaire de modifier cette topologie pour l’adapter à une utilisation d’Azure Data Lake Store avec les autres clusters Apache Storm.

## <a name="how-to-work-with-azure-data-lake-store"></a>Utilisation d’Azure Data Lake Store

Data Lake Store est perçu par HDInsight comme un système de fichiers HDFS compatible. Vous pouvez donc utiliser le bolt Storm-HDFS pour y écrire. Le code suivant montre comment vous pouvez utiliser le bolt Storm-HDFS pour écrire des données dans un répertoire nommé `/stormdata` sur un compte Data Lake Store nommé `MYDATALAKE`.

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

> [!IMPORTANT]
> Lorsque vous utilisez un Data Lake Store à partir d’HDInsight, utilisez le modèle d’URI `adl://`.

## <a name="prerequisites"></a>Composants requis

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou ultérieure

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un cluster Storm sur HDInsight version 3.2. Pour créer un cluster Storm sur HDInsight, suivez les étapes du document [Approvisionner un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) . Les étapes décrites dans ce document vous guideront dans la création d’un nouveau cluster HDInsight et d’une instance Azure Data Lake Store.  
  
  > [!IMPORTANT]
  > Lorsque vous créez le cluster HDInsight, vous devez sélectionner **Storm** comme type de cluster et **3.2** comme version. Le système d’exploitation peut être Windows ou Linux.

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`.
* **PATH** :doit contenir les chemins d’accès suivants :
  
  * **JAVA\_HOME** (ou le chemin d’accès équivalent)
  * **JAVA\_HOME\bin** (ou le chemin d’accès équivalent)
  * Le répertoire d’installation de Maven

## <a name="topology-implementation"></a>Implémentation de la topologie

L’exemple utilisé dans ce document est écrit en Java et utilise les composants suivants :

* **TickSpout** : génère les données utilisées par d’autres composants de la topologie.
* **PartialCount** : dénombre les événements générés par TickSpout.
* **FinalCount** : agrège les données de comptage de PartialCount.
* **ADLStoreBolt** : écrit des données dans Azure Data Lake Store à l’aide du composant [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

Le projet contenant cette topologie est disponible en téléchargement à partir de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

### <a name="understanding-adlstorebolt"></a>Présentation d’ADLStoreBolt

ADLStoreBolt est le nom utilisé pour l’instance HdfsBolt de la topologie qui écrit dans Azure Data Lake. Il ne s’agit pas d’une version spéciale de HdfsBolt créée par Microsoft ; en revanche, il repose sur les valeurs de configuration du site de base, ainsi que sur les composants Hadoop inclus avec Azure HDInsight pour communiquer avec Azure Data Lake.

En particulier, lorsque vous créez un cluster HDInsight, vous pouvez l’associer à une instance Azure Data Lake Store. ADLStoreBolt écrit des entrées dans le site de base pour le Data Lake Store sélectionné, lesquelles sont utilisées par des composants tels que hadoop-client et hadoop-hdfs pour permettre la communication avec Azure Data Lake Store.

> [!NOTE]
> Microsoft a contribué au développement de code pour les projets Apache Hadoop et Storm qui permettent une communication avec Azure Data Lake Store et Azure Blob Storage, mais cette fonctionnalité n’est pas forcément incluse par défaut dans les autres distributions Hadoop et Storm.

HdfsBolt est configuré comme suit dans la topologie :

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
        .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
          .withRecordFormat(recordFormat)
          .withFileNameFormat(fileNameFormat)
          .withRotationPolicy(rotationPolicy)
          .withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");

Si vous maîtrisez l’utilisation de HdfsBolt, vous remarquerez qu’il s’agit d’une configuration relativement standard, à l’exception de l’URL. L’URL fournit le chemin d’accès à la racine de votre instance Azure Data Lake Store.

Puisque l’écriture dans le Data Lake Store utilise HdfsBolt et implique simplement un changement d’URL, vous devriez être en mesure de partir de n’importe quelle topologie existante qui écrit dans HDFS ou WASB à l’aide de HdfsBolt et de la modifier facilement pour pouvoir l’utiliser avec Azure Data Lake Store.

## <a name="build-and-package-the-topology"></a>Génération et empaquetage de la topologie

1. Téléchargez l’exemple de projet à partir de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) dans votre environnement de développement.

2. Ouvrez le fichier `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` dans un éditeur, puis recherchez la ligne contenant `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Remplacez **MYDATALAKE** par le nom de l’instance Azure Data Lake Store que vous avez utilisée lors de la création de votre serveur HDInsight.

3. À partir d’une invite de commandes, d’un terminal ou d’une session shell, modifiez les répertoires à la racine du projet téléchargé et exécutez les commandes suivantes pour générer et empaqueter la topologie.
   
        mvn compile
        mvn package
   
    Une fois la génération et l’empaquetage terminés, vous obtiendrez un répertoire nommé `target`, qui contient un fichier `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Ce fichier contient la topologie compilée.

## <a name="deploy-and-run-on-linux-based-hdinsight"></a>Déploiement et exécution sur un cluster HDInsight basé sur Linux

Si vous avez créé un cluster Storm sur HDInsight basé sur Linux, procédez comme suit pour déployer et exécuter la topologie.

1. Utilisez la commande suivante pour copier la topologie sur le cluster HDInsight. Remplacez **USER** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
   
   > [!NOTE]
   > Si vous utilisez un client Windows pour le développement, vous ne disposerez peut-être pas d’une commande `scp`. Dans ce cas, vous pouvez utiliser l’utilitaire `pscp`disponible à l’adresse [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Une fois le téléchargement terminé, utilisez les éléments suivants pour vous connecter au cluster HDInsight à l’aide de SSH. Remplacez **USER** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
   
   > [!NOTE]
   > Si vous utilisez un client Windows pour le développement, suivez les instructions de l’article [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour savoir comment utiliser le client PuTTY pour se connecter au cluster.

3. Une fois connecté, procédez comme suit pour démarrer la topologie :
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
   
    La topologie démarre sous le nom convivial `datalakewriter`.

## <a name="deploy-and-run-on-windows-based-hdinsight"></a>Déploiement et exécution sur un cluster HDInsight basé sur Windows

1. Dans un navigateur web, accédez à l’adresse HTTPS://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** est le nom de votre cluster HDInsight. À l’invite, indiquez le nom d’utilisateur admin (`admin`) et le mot de passe que vous avez utilisés pour ce compte lors de la création du cluster.

2. Dans le tableau de bord Storm, sélectionnez **Parcourir** à partir de la liste déroulante **Fichier Jar**, puis sélectionnez le fichier StormToDataLakeStore-1.0-SNAPSHOT.jar dans le répertoire `target`. Utilisez les valeurs suivantes pour les autres entrées du formulaire :
   
   * Nom de la classe : com.microsoft.example.StormToDataLakeStore
   * Paramètres supplémentaires : datalakewriter
     
  ![image du tableau de bord storm](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Cliquez sur le bouton **Envoyer** pour télécharger et démarrer la topologie. Une fois la topologie démarrée, le champ de résultat situé sous le bouton **Envoyer** doit afficher des informations similaires à ce qui suit :
   
        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

## <a name="view-output-data"></a>Affichage des données de sortie

Il existe plusieurs façons de visualiser les données. Dans cette section, nous utilisons le portail Azure et la commande `hdfs` pour afficher les données.

> [!NOTE]
> Vous devez laisser les topologies s’exécuter pendant plusieurs minutes avant de vérifier les données de sortie, afin que les données soient synchronisées avec plusieurs fichiers sur Azure Data Lake Store.


* **Depuis le [portail Azure](https://portal.azure.com)** : dans le portail, sélectionnez l’Azure Data Lake Store que vous avez utilisé avec HDInsight.
  
  > [!NOTE]
  > Si vous n’avez pas épinglé le Data Lake Store au tableau de bord du portail Azure, vous pouvez le retrouver en sélectionnant **Parcourir** en bas de la liste à gauche, puis **Data Lake Store**, et enfin en sélectionnant le Store concerné.
  
    Parmi les icônes situées en haut du Data Lake Store, sélectionnez **Explorateur de données**.
  
    ![icône d’exploration des données](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
  
    Ensuite, sélectionnez le dossier **stormdata**. Une liste des fichiers texte doit s’afficher.
  
    ![fichiers texte](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
  
    Sélectionnez un des fichiers pour en afficher le contenu.

* **Depuis le cluster** : si vous vous êtes connecté au cluster HDInsight à l’aide de SSH (cluster Linux) ou du Bureau à distance (cluster Windows), vous pouvez utiliser les éléments suivants pour afficher les données. Remplacez **DATALAKE** par le nom de votre Data Lake Store.
  
        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt
  
    Cette commande va concaténer les fichiers texte stockés dans le répertoire et afficher des informations similaires à ce qui suit :
  
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000

## <a name="stop-the-topology"></a>Arrêt de la topologie

Les topologies Storm s’exécutent jusqu’à ce qu’elles soient arrêtées ou que le cluster soit supprimé. Utilisez les informations suivantes pour arrêter les topologies :

**Pour un cluster HDInsight sous Linux** :

Depuis une session SSH vers le cluster, utilisez la commande suivante :

    storm kill datalakewriter

**Pour un cluster HDInsight sous Windows** :

1. Dans le tableau de bord Storm (https://CLUSTERNAME.azurehdinsight.net), sélectionnez le lien **Interface utilisateur de Storm** en haut de la page.

2. Une fois l’interface utilisateur de Storm chargée, cliquez sur le lien **datalakewriter**.
   
    ![lien vers datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. Dans la section **Actions de topologie**, sélectionnez **Supprimer**, puis cliquez sur OK dans la boîte de dialogue qui s’affiche.
   
    ![actions de topologie](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Storm pour écrire dans Azure Data Lake Store, découvrez d’autres [exemples Storm pour HDInsight](hdinsight-storm-example-topology.md).




<!--HONumber=Jan17_HO3-->


