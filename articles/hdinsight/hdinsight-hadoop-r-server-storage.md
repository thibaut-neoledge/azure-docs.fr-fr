<properties
   pageTitle="Options Azure Storage pour R Server sur HDInsight Premium | Azure"
   description="Découvrez les différentes options de stockage disponibles pour les utilisateurs avec R Server sur HDInsight Premium"
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
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# Options d’Azure Storage pour R Server sur HDInsight Premium

Un serveur R sur un cluster HDI a accès aux objets blob Azure et aura bientôt accès au stockage Azure Data Lake comme moyen de conserver les données, le code, les objets de résultats d’analyse, etc.

Lors de la création d’un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d’objets blob spécifique de ce compte est désigné pour contenir le système de fichiers du cluster que vous créez, à savoir le système de fichiers distribué Hadoop (HDFS). Pour des raisons de performances, le cluster HDInsight est créé dans le même centre de données que le compte de stockage principal que vous spécifiez. Pour plus d'informations, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md "Utilisation du stockage d'objets blob Azure avec HDInsight").


## Utiliser plusieurs comptes de stockage d’objets blob Azure supplémentaires

Si nécessaire, il est possible d’accéder à plusieurs conteneurs ou comptes de stockage Azure avec votre cluster HDI. Pour cela, vous devez spécifier les comptes de stockage supplémentaires dans l’interface utilisateur au moment de la création du cluster et procéder comme suit pour les utiliser dans R.

1.	Supposons que vous créez un cluster HDInsight avec un nom de compte de stockage « storage1 » avec le conteneur par défaut « container1 ». Vous spécifiez également un compte de stockage supplémentaire appelé « stockage2 ».  
2.	Vous maintenant copiez un fichier « mycsv.csv » dans le répertoire «/share » et vous souhaitez effectuer une analyse sur le fichier.  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	Dans le code R, vous définissez le nœud du nom sur « default » et vous définissez le répertoire et le fichier à traiter

    myNameNode <- "default" myPort <- 0

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

Supposons maintenant que vous souhaitez traiter un fichier appelé « mySpecial.csv » qui se trouve dans le répertoire « /private » du conteneur « container2 », sur le nom du compte de stockage « storage2 ».

Dans votre code R, vous modifiez la référence du nœud de nom vers le compte de stockage « storage2 ».

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

Notez que vous devez configurer le répertoire de la base de données/User/RevoShare/<SSH username> sur le compte de stockage « storage2 » :

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## Utiliser des fichiers Azure sur le nœud Edge 

Il existe également une option de stockage de données pratique pour une utilisation sur le nœud Edge, appelée [Azure Files](../storage/storage-how-to-use-files-linux.md "Azure Files"), et qui vous permet de monter un partage de fichiers Azure Storage dans le système de fichiers Linux. Cela peut être pratique pour stocker des fichiers de données, des scripts R et des objets de résultats qui peuvent être nécessaires ultérieurement, lorsqu’il sera judicieux d’utiliser le système de fichiers natif sur le nœud Edge plutôt que le HDFS. Un grand avantage de l’utilisation de fichiers Azure est que les partages de fichiers peuvent être montés et utilisés par n’importe quel système avec un système d’exploitation pris en charge (Windows, Linux), par exemple un autre cluster HDInsight dont vous ou une autre personne de votre équipe disposez, une machine virtuelle Azure, voire même un système local.


## Étapes suivantes

Maintenant que vous savez comment créer un cluster HDInsight incluant R Server et connaissez les rudiments de l’utilisation de la console R à partir d’une session SSH, utilisez les informations suivantes pour découvrir d’autres manière d’utiliser R Server sur HDInsight.

- [Vue d’ensemble de R Server sur Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Prise en main de R Server sur Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter RStudio Server à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Options de contexte de calcul pour R Server sur HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0330_2016-->