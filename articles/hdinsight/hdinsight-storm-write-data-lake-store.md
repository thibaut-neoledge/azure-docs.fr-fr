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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 514319dfcb532ab3708352b2467c095d7775b714
ms.lasthandoff: 03/25/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight (Java)

Azure Data Lake Store est un service de stockage cloud compatible HDFS qui offre à vos données un haut niveau de débit, de disponibilité, de durabilité et de fiabilité. Dans ce document, vous apprenez à utiliser une topologie Storm basée sur Java pour écrire des données dans Azure Data Lake Store. Les étapes décrites dans ce document utilisent le composant [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), qui est fourni avec Apache Storm.

> [!IMPORTANT]
> L’exemple de topologie utilisé dans ce document repose sur les composants inclus avec Storm sur les clusters HDInsight. Il sera peut-être nécessaire de modifier cette topologie pour l’adapter à une utilisation d’Azure Data Lake Store avec les autres clusters Apache Storm.

## <a name="how-to-work-with-azure-data-lake-store"></a>Utilisation d’Azure Data Lake Store

Data Lake Store est perçu par HDInsight comme un système de fichiers HDFS compatible. Vous pouvez donc utiliser le bolt Storm-HDFS pour y écrire. Lorsque vous utilisez Azure Data Lake à partir de HDInsight, vous pouvez utiliser un schéma de fichier à partir de `adl://`.

* Si Data Lake Storage est le stockage principal pour le cluster, utilisez `adl:///`. Il s’agit de la racine du stockage du cluster dans Azure Data Lake. Cela peut mener à un chemin d’accès de type /clusters/CLUSTERNAME dans votre compte Data Lake Storage.
* Si Data Lake Storage est le stockage secondaire pour le cluster, utilisez `adl://DATALAKEACCOUNT.azuredatalakestore.net/`. Cette URI spécifie le compte Data Lake Storage dans lequel les données sont écrites. Les données sont écrites en commençant à la racine de Data Lake Store.

    > [!NOTE]
    > Vous pouvez également utiliser ce format d’URI pour enregistrer des données dans le compte Data Lake Store qui contient le stockage principal de votre cluster. Cela vous permet d’enregistrer les données en dehors du chemin d’accès au répertoire contenant HDInsight.

Le code Java suivant montre comment vous pouvez utiliser le bolt Storm-HDFS pour écrire des données dans un répertoire nommé `/stormdata` sur un compte Data Lake Store nommé `MYDATALAKE`.

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

Le YAML suivant montre comment utiliser le bolt Storm-HDFS à partir de l’infrastructure Flux :

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1
```

> [!NOTE]
> Les exemples de ce document utilisent l’infrastructure Flux.

## <a name="prerequisites"></a>Composants requis

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou version ultérieure. HDInsight 3.5 nécessite Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un cluster Storm sur HDInsight version 3.5. Pour créer un cluster Storm sur HDInsight, suivez les étapes du document [Approvisionner un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) .

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-8-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.8`.
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

## <a name="build-and-package-the-topology"></a>Génération et empaquetage de la topologie

1. Téléchargez l’exemple de projet à partir de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) dans votre environnement de développement.

2. Ouvrez le fichier `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` dans un éditeur, puis recherchez la ligne contenant `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Remplacez **MYDATALAKE** par le nom de l’instance Azure Data Lake Store que vous avez utilisée lors de la création de votre serveur HDInsight.

3. À partir d’une invite de commandes, d’un terminal ou d’une session shell, modifiez les répertoires à la racine du projet téléchargé et exécutez les commandes suivantes pour générer et empaqueter la topologie.
   
        mvn compile
        mvn package
   
    Une fois la génération et l’empaquetage terminés, vous obtiendrez un répertoire nommé `target`, qui contient un fichier `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Ce fichier contient la topologie compilée.

## <a name="deploy-and-run-the-topology"></a>Déployer et exécuter la topologie

1. Utilisez la commande suivante pour copier la topologie sur le cluster HDInsight. Remplacez **USER** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
   
   > [!NOTE]
   > Si vous utilisez un client Windows pour le développement, vous ne disposerez peut-être pas d’une commande `scp`. Dans ce cas, vous pouvez utiliser l’utilitaire `pscp`disponible à l’adresse [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Une fois le téléchargement terminé, utilisez les éléments suivants pour vous connecter au cluster HDInsight à l’aide de SSH. Remplacez **USER** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
   
   Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Une fois connecté, utilisez la commande suivante pour créer un fichier nommé `dev.properties` :

        nano dev.properties

4. Utilisez les données suivantes comme contenu du fichier `dev.properties` :

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    Pour enregistrer le fichier, use __Ctrl + X__, puis __Y__, et enfin __Entrée__. Les valeurs dans ce fichier définissent l’URL de Data Lake Store et le nom du répertoire où sont écrites les données.

3. Utilisez la commande suivante pour démarrer la topologie :
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    Cette commande démarre la topologie à l’aide de l’infrastructure Flux. La topologie est définie par le fichier `datalakewriter.yaml` inclus dans le fichier jar. Le fichier `dev.properties` est transféré en tant que filtre et les valeurs qu’il contient sont lues par la topologie.

## <a name="view-output-data"></a>Affichage des données de sortie

Pour afficher les données, utilisez la commande suivante :

    hdfs dfs -ls /stormdata/

Cela affiche une liste des fichiers qui ont été créés par la topologie.

Si Data Lake Store n’est pas le stockage par défaut pour le cluster, utilisez la commande suivante pour afficher les données :

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

Dans la commande précédente, remplacez __MYDATALAKE__ par le compte Data Lake Store.

La liste suivante est un exemple des données retournées par les commandes précédentes :

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>Arrêt de la topologie

Les topologies Storm s’exécutent jusqu’à ce qu’elles soient arrêtées ou que le cluster soit supprimé. Utilisez les informations suivantes pour arrêter les topologies :

**Pour un cluster HDInsight sous Linux** :

Depuis une session SSH vers le cluster, utilisez la commande suivante :

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Storm pour écrire dans Azure Data Lake Store, découvrez d’autres [exemples Storm pour HDInsight](hdinsight-storm-example-topology.md).


