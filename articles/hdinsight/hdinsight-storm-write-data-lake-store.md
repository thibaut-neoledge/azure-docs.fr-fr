<properties
pageTitle="Utilisation d’Azure Data Lake Store avec Apache Storm sur Azure HDInsight"
description="Apprenez à écrire des données dans Azure Data Lake Store à partir d’une topologie Apache Storm sur HDInsight. Ce document et l’exemple associé illustrent la manière dont le composant HdfsBolt peut être utilisé pour écrire des données dans le Data Lake Store."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/28/2016"
ms.author="larryfr"/>

#Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight

Azure Data Lake Store est un service de stockage cloud compatible HDFS qui offre à vos données un haut niveau de débit, de disponibilité, de durabilité et de fiabilité. Dans ce document, vous allez apprendre à utiliser une topologie Storm basée sur Java pour écrire des données dans Azure Data Lake Store à l’aide du composant [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) fourni avec Apache Storm.

> [AZURE.IMPORTANT] L’exemple de topologie utilisé dans ce document repose sur les composants inclus avec Storm sur les clusters HDInsight. Il sera peut-être nécessaire de modifier cette topologie pour l’adapter à une utilisation d’Azure Data Lake Store avec les autres clusters Apache Storm.

##Configuration requise

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou ultérieure
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Un abonnement Azure
* Un cluster Storm sur HDInsight Ce document contient des informations sur la création d’un cluster pouvant utiliser Azure Data Lake Store.

###Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* __JAVA\_HOME__ : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Dans Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`.

* __PATH__ :doit contenir les chemins d’accès suivants :

    * __JAVA\_HOME__ (ou le chemin d’accès équivalent)
    
    * __JAVA\_HOME\\bin__ (ou le chemin d’accès équivalent)
    
    * Le répertoire d’installation de Maven

##Implémentation de la topologie

L’exemple utilisé dans ce document est écrit en Java et utilise les composants suivants :

* __TickSpout__ : génère les données utilisées par d’autres composants de la topologie.

* __PartialCount__ : dénombre les événements générés par TickSpout.

* __FinalCount__ : agrège les données de comptage de PartialCount.

* __ADLStoreBolt__ : écrit des données dans Azure Data Lake Store à l’aide du composant [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

Le projet contenant cette topologie est disponible en téléchargement à partir de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

###Présentation d’ADLStoreBolt

ADLStoreBolt est le nom utilisé pour l’instance HdfsBolt de la topologie qui écrit dans Azure Data Lake. Il ne s’agit pas d’une version spéciale de HdfsBolt créée par Microsoft ; en revanche, il repose sur les valeurs de configuration du site de base, ainsi que sur les composants Hadoop inclus avec Azure HDInsight pour communiquer avec Azure Data Lake.

En particulier, lorsque vous créez un cluster HDInsight, vous pouvez l’associer à une instance Azure Data Lake Store. ADLStoreBolt écrit des entrées dans le site de base pour le Data Lake Store sélectionné, lesquelles sont utilisées par des composants tels que hadoop-client et hadoop-hdfs pour permettre la communication avec Azure Data Lake Store.

> [AZURE.NOTE] Microsoft a contribué au développement de code pour les projets Apache Hadoop et Storm qui permettent une communication avec Azure Data Lake Store et Azure Blob Storage, mais cette fonctionnalité n’est pas forcément incluse par défaut dans les autres distributions Hadoop et Storm.

HdfsBolt est configuré comme suit dans la topologie :

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

##Création d’un cluster HDInsight et d’un Data Lake Store

Créez un cluster Storm sur HDInsight en suivant les étapes du document [Approvisionner un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Les étapes décrites dans ce document vous guideront dans la création d’un nouveau cluster HDInsight et d’une instance Azure Data Lake Store.

> [AZURE.IMPORTANT] Lorsque vous créez le cluster HDInsight, vous devez sélectionner __Storm__ comme type de cluster. Le système d’exploitation peut être Windows ou Linux.

##Génération et empaquetage de la topologie

1. Téléchargez l’exemple de projet à partir de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) dans votre environnement de développement.

2. Ouvrez le fichier `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` dans un éditeur, puis recherchez la ligne contenant `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Remplacez __MYDATALAKE__ par le nom de l’instance Azure Data Lake Store que vous avez utilisé lors de la création de votre serveur HDInsight.

3. À partir d’une invite de commandes, d’un terminal ou d’une session shell, modifiez les répertoires à la racine du projet téléchargé et exécutez les commandes suivantes pour générer et empaqueter la topologie.

        mvn compile
        mvn package
    
    Une fois la génération et l’empaquetage terminés, vous obtiendrez un répertoire nommé `target`, qui contient un fichier `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Ce fichier contient la topologie compilée.

##Déploiement et exécution sur un cluster HDInsight basé sur Linux

Si vous avez créé un cluster Storm sur HDInsight basé sur Linux, procédez comme suit pour déployer et exécuter la topologie.

1. Utilisez la commande suivante pour copier la topologie sur le cluster HDInsight. Remplacez __USER__ par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez __CLUSTERNAME__ par le nom du cluster.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
    
    > [AZURE.NOTE] Si vous utilisez un client Windows pour le développement, vous ne disposerez peut-être pas d’une commande `scp`. Dans ce cas, vous pouvez utiliser l’utilitaire `pscp` disponible à l’adresse [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Une fois le téléchargement terminé, utilisez les éléments suivants pour vous connecter au cluster HDInsight à l’aide de SSH. Remplacez __USER__ par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez __CLUSTERNAME__ par le nom du cluster.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
    
    > [AZURE.NOTE] Si vous utilisez un client Windows pour le développement, suivez les instructions de l’article [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour savoir comment utiliser le client PuTTY pour se connecter au cluster.
    
3. Une fois connecté, procédez comme suit pour démarrer la topologie :

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    La topologie démarre sous le nom convivial `datalakewriter`.

##Déploiement et exécution sur un cluster HDInsight basé sur Windows

1. Ouvrez un navigateur web et accédez à HTTPS://CLUSTERNAME.azurehdinsight.net, où __CLUSTERNAME__ est le nom de votre cluster HDInsight. À l’invite, indiquez le nom d’utilisateur admin (`admin`) et le mot de passe que vous avez utilisés pour ce compte lors de la création du cluster.

2. Dans le tableau de bord Storm, sélectionnez __Parcourir__ à partir de la liste déroulante __Fichier Jar__, puis sélectionnez le fichier StormToDataLakeStore-1.0-SNAPSHOT.jar dans le répertoire `target`. Utilisez les valeurs suivantes pour les autres entrées du formulaire :

    * Nom de la classe : com.microsoft.example.StormToDataLakeStore
    * Paramètres supplémentaires : datalakewriter
    
    ![image du tableau de bord storm](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Cliquez sur le bouton __Envoyer__ pour télécharger et démarrer la topologie. Une fois la topologie démarrée, le champ de résultat situé sous le bouton __Envoyer__ doit afficher des informations similaires à ce qui suit :

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##Affichage des données de sortie

Il existe plusieurs façons de visualiser les données. Dans cette section, nous utilisons le portail Azure et la commande `hdfs` pour afficher les données.

> [AZURE.NOTE] Vous devez laisser les topologies s’exécuter pendant plusieurs minutes avant de vérifier les données de sortie, afin que les données soient synchronisées avec plusieurs fichiers sur Azure Data Lake Store.

* __Depuis le [portail Azure](https://portal.azure.com)__ : dans le portail, sélectionnez l’Azure Data Lake Store que vous avez utilisé avec HDInsight.

    > [AZURE.NOTE] Si vous n’avez pas épinglé le Data Lake Store au tableau de bord du portail Azure, vous pouvez le retrouver en sélectionnant __Parcourir__ en bas de la liste à gauche, puis __Data Lake Store__, et enfin en sélectionnant le Store concerné.
    
    Parmi les icônes situées en haut du Data Lake Store, sélectionnez __Explorateur de données__.
    
    ![icône d’exploration des données](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    Ensuite, sélectionnez le dossier __stormdata__. Une liste des fichiers texte doit s’afficher.
    
    ![fichiers texte](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Sélectionnez un des fichiers pour en afficher le contenu.

* __Depuis le cluster__ : si vous vous êtes connecté au cluster HDInsight à l’aide de SSH (cluster Linux) ou du Bureau à distance (cluster Windows), vous pouvez utiliser les éléments suivants pour afficher les données. Remplacez __DATALAKE__ par le nom de votre Data Lake Store

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    Cette commande va concaténer les fichiers texte stockés dans le répertoire et afficher des informations similaires à ce qui suit :
    
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
        
##Arrêt de la topologie

Les topologies Storm s’exécutent jusqu’à ce qu’elles soient arrêtées ou que le cluster soit supprimé. Utilisez les informations suivantes pour arrêter les topologies :

__Pour un cluster HDInsight basé sur Linux__ :

Depuis une session SSH vers le cluster, utilisez la commande suivante :

    storm kill datalakewriter

__Pour un cluster HDInsight basé sur Windows__ :

1. Dans le Tableau de bord Storm (https://CLUSTERNAME.azurehdinsight.net,), sélectionnez __Interface utilisateur de Storm__ en haut de la page.

2. Une fois l’interface utilisateur de Storm chargée, cliquez sur le lien __datalakewriter__.

    ![lien vers datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. Dans la section __Actions de topologie__, sélectionnez __Supprimer__ puis cliquez sur OK dans la boîte de dialogue qui s’affiche.

    ![actions de topologie](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## Supprimer votre cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Étapes suivantes

Ayant appris à utiliser Storm pour écrire dans Azure Data Lake Store, découvrez d’autres [exemples Storm pour HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0309_2016-->