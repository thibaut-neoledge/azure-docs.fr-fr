<properties
   pageTitle="Options d’Azure Storage pour R Server sur HDInsight (version préliminaire) | Azure"
   description="Découvrez les différentes options de stockage disponibles pour les utilisateurs avec R Server sur HDInsight (version préliminaire)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="jeffstok"
/>

# Options d’Azure Storage pour R Server sur HDInsight (version préliminaire)

Un serveur R sur HDInsight (version préliminaire) a accès aux objets blob Azure et à [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) comme moyen de conserver les données, le code, les objets de résultats d’analyse, etc.

Lors de la création d’un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d’objets blob spécifique de ce compte est désigné pour contenir le système de fichiers du cluster que vous créez, à savoir le système de fichiers distribué Hadoop (HDFS). Pour des raisons de performances, le cluster HDInsight est créé dans le même centre de données que le compte de stockage principal que vous spécifiez. Pour plus d'informations, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md "Utilisation du stockage d'objets blob Azure avec HDInsight").


## Utiliser plusieurs comptes de stockage d’objets blob Azure supplémentaires

Si nécessaire, il est possible d’accéder à plusieurs conteneurs ou comptes de stockage Azure avec votre cluster HDI. Pour cela, vous devez spécifier les comptes de stockage supplémentaires dans l’interface utilisateur au moment de la création du cluster et procéder comme suit pour les utiliser dans R.

1.	Supposons que vous créez un cluster HDInsight avec un nom de compte de stockage « storage1 » avec le conteneur par défaut « container1 ». Vous spécifiez également un compte de stockage supplémentaire appelé « stockage2 ».  
2.	Vous maintenant copiez un fichier « mycsv.csv » dans le répertoire «/share » et vous souhaitez effectuer une analyse sur le fichier.  

````
hadoop fs –mkdir /share
hadoop fs –copyFromLocal myscsv.scv /share  
````

3.	Dans le code R, vous définissez le nœud du nom sur « default » et vous définissez le répertoire et le fichier à traiter  

````
myNameNode <- "default"
myPort <- 0
````

  Emplacement des données

    bigDataDirRoot <- "/share"  

  définir le contexte de calcul Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  définir le contexte de calcul

    rxSetComputeContext(mySparkCluster)

  définir le système de fichiers HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  spécifier le fichier HDFS à analyser

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
Toutes les références de fichier et de répertoire pointent vers le compte de stockage wasb://container1@storage1.blob.core.windows.net, car il s’agit du **compte de stockage par défaut** associé au cluster HDInsight.

Supposons maintenant que vous souhaitez traiter un fichier appelé « mySpecial.csv » qui se trouve dans le répertoire « /private » du conteneur « container2 », sur le nom du compte de stockage « storage2 ».

Dans votre code R, vous modifiez la référence du nœud de nom vers le compte de stockage « storage2 ».

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Emplacement des données

    bigDataDirRoot <- "/private"

  définir le contexte de calcul Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  définir le contexte de calcul

    rxSetComputeContext(mySparkCluster)

  définir le système de fichiers HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  spécifier le fichier HDFS à analyser

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
Toutes les références de répertoire et de fichier pointent maintenant vers le compte de stockage wasb://container2@storage2.blob.core.windows.net, car il s’agit du **nœud de nom** que vous avez spécifié.

Notez que vous devez configurer le répertoire /user/RevoShare/<SSH username> sur le compte de stockage « storage2 » :

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Utilisation d’un Azure Data Lake Store

Pour utiliser des magasins Azure Data Lake avec votre compte de HDInsight, vous devez autoriser votre cluster à accéder à chaque Azure Data Lake Store que vous souhaitez utiliser, puis référencer le magasin de votre script R d’une manière similaire à l’utilisation d’un compte de stockage Azure secondaire décrite ci-dessus.

## Ajout d’un accès du cluster d’accéder à vos magasins Azure Data Lake

L’accès à un Azure Data Lake Store est établi avec l’utilisation d’un principal de Service Azure Active Directory (AAD) associé à votre cluster HDInsight. Pour ajouter un principal du service lors de la création de votre cluster HDInsight, cliquez sur l’option « Identité AAD de cluster » dans l’onglet Source de données et cliquez sur l’option de création d’un principal du service. Après avoir attribué un nom et un mot de passe, un nouvel onglet s’ouvre pour vous permettre d’associer le principal du service à vos magasins Azure Data Lake.

Notez que vous pouvez également ajouter l’accès à un Azure Data Lake Store ultérieurement en ouvrant l’Azure Data Lake Store dans le portail Azure et en accédant à « Explorateur de données -> Accès ». Voici un exemple de dialogue qui montre la création d’un principal du Service et son association à l’Azure Data Lake Store « rkadl11 ».

![Création du principal du service 1 pour le service ADL Store](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Création du principal du service 2 pour le service ADL Store](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## Utilisation d’Azure Data Lake Store avec R Server
Une fois que vous avez donné accès à un Azure Data Lake Store à l’aide du principal du service du cluster, vous pouvez l’utiliser dans le serveur R sur HDInsight de la même manière en tant que compte de stockage secondaire. La seule différence est, par exemple, que le préfixe wasb:// devient adl://.

````
# point to the ADL store (e.g. ADLtest) 
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account) 
bigDataDirRoot <- "/share"  

# define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE)

# set compute context
rxSetComputeContext(mySparkCluster)

# define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# create Factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# define the data source 
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

> [AZURE.NOTE] Voici les commandes utilisées pour configurer le compte de stockage Azure Data Lake avec le répertoire RevoShare et ajouter l’exemple de fichier CSV pour l’exemple ci-dessus :

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## Utiliser des fichiers Azure sur le nœud Edge 

Il existe également une option de stockage de données pratique pour une utilisation sur le nœud périphérie, appelée [Azure Files](../storage/storage-how-to-use-files-linux.md "Azure Files"). Elle vous permet de créer un partage de fichiers Azure Storage dans le système de fichiers Linux. Cela peut être pratique pour stocker des fichiers de données, des scripts R et des objets de résultats qui peuvent être nécessaires ultérieurement, lorsqu’il sera judicieux d’utiliser le système de fichiers natif sur le nœud Edge plutôt que le HDFS. Un grand avantage de l’utilisation de fichiers Azure est que les partages de fichiers peuvent être montés et utilisés par n’importe quel système avec un système d’exploitation pris en charge (Windows, Linux), par exemple un autre cluster HDInsight dont vous ou une autre personne de votre équipe disposez, une machine virtuelle Azure, voire même un système local.


## Étapes suivantes

Maintenant que vous savez comment créer un cluster HDInsight incluant R Server et connaissez les rudiments de l’utilisation de la console R à partir d’une session SSH, utilisez les informations suivantes pour découvrir d’autres manière d’utiliser R Server sur HDInsight.

- [Vue d’ensemble de R Server sur Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Prise en main de R Server sur Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter RStudio Server à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Options de contexte de calcul pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0608_2016-->