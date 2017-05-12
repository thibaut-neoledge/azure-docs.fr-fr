---
title: "Options d’Azure Storage pour R Server sur HDInsight | Microsoft Docs"
description: "Découvrez les différentes options de stockage disponibles pour les utilisateurs avec R Server sur HDInsight"
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 18dcb3a319f78639b27f9e70a2177423192e5958
ms.contentlocale: fr-fr
ms.lasthandoff: 04/13/2017


---
# <a name="azure-storage-options-for-r-server-on-hdinsight"></a>Options d’Azure Storage pour R Server sur HDInsight
Microsoft R Server sur HDInsight a accès aux objets blob Azure et à [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) pour conserver les données, le code, les objets de résultats d’analyse, etc.

Lors de la création d’un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage ou un magasin Data Lake. Un conteneur de stockage spécifique de ce compte contient le système de fichiers du cluster que vous créez (par exemple, le système Hadoop Distributed File System). Pour des raisons de performances, le cluster HDInsight est créé dans le même centre de données que le compte de stockage principal que vous spécifiez. Pour plus d'informations, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md "Utilisation du stockage d’objets blob Azure avec HDInsight").   

## <a name="use-multiple-azure-blob-storage-accounts"></a>Utiliser plusieurs comptes de stockage d’objets blob Azure
Si nécessaire, vous pouvez accéder à plusieurs conteneurs ou comptes de stockage Azure avec votre cluster HDI. Pour cela, vous devez spécifier les comptes de stockage supplémentaires dans l’interface utilisateur au moment de la création du cluster et procéder comme suit pour les utiliser dans R.

> [!WARNING]
> L’utilisation d’un compte de stockage dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

1. Créez un cluster HDInsight avec un nom de compte de stockage **storage1** et un conteneur par défaut appelé **container1**.
2. Spécifiez un compte de stockage supplémentaire appelé **storage2**.  
3. Copiez le fichier mycsv.csv dans le répertoire /share et effectuez une analyse sur ce fichier.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. Dans le code R, définissez le nœud du nom sur **par défaut** et définissez le répertoire et le fichier à traiter.  

        myNameNode <- "default"
        myPort <- 0

    Emplacement des données :  

        bigDataDirRoot <- "/share"  

    Définir le contexte de calcul Spark :

        mySparkCluster <- RxSpark(consoleOutput=TRUE)

    Définir le contexte de calcul :

        rxSetComputeContext(mySparkCluster)

    Définir le système de fichiers Hadoop Distributed File System (HDFS) :

        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    Spécifier le fichier d’entrée à analyser dans HDFS :

        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Toutes les références de fichier et de répertoire pointent vers le compte de stockage wasbs://container1@storage1.blob.core.windows.net. Il s’agit du **compte de stockage par défaut** associé au cluster HDInsight.

Supposons maintenant que vous souhaitiez traiter un fichier appelé mySpecial.csv qui se trouve dans le répertoire /private du conteneur **container2** dans le compte de stockage **storage2**.

Dans votre code R, pointez la référence du nœud de nom vers le compte de stockage **storage2** .

````
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0
````

Emplacement des données :

````
bigDataDirRoot <- "/private"
````

Définir le contexte de calcul Spark :

````
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)
````

Définir le contexte de calcul :

````
rxSetComputeContext(mySparkCluster)
````

Définir le système de fichiers HDFS :

````
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)
````

Spécifier le fichier d’entrée à analyser dans HDFS :

````
inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
````

Toutes les références de fichier et de répertoire pointent désormais vers le compte de stockage wasbs://container2@storage2.blob.core.windows.net. Il s’agit du **nom de nœud** que vous avez spécifié.

Notez que vous devez configurer le répertoire /user/RevoShare/<SSH username> sur **storage2** comme suit :

````
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
````

## <a name="use-an-azure-data-lake-store"></a>Utiliser un magasin Azure Data Lake
Pour utiliser des magasins Data Lake avec votre compte HDInsight, vous devez permettre à votre cluster d’accéder à tous les magasins Azure Data Lake que vous souhaitez utiliser. Vous utilisez le magasin dans votre script R de la même manière qu’un compte de stockage secondaire (comme décrit dans la procédure précédente).

## <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Ajouter un accès de cluster à vos magasins Azure Data Lake
Vous accédez à un magasin Data Lake en utilisant un principal du service Azure Active Directory (Azure AD) associé à votre cluster HDInsight.

### <a name="to-add-a-service-principal"></a>Pour ajouter un principal du service
1.Lorsque vous créez votre cluster HDInsight, sélectionnez **Identité AAD de cluster** à partir de l’onglet **Source de données**.

2.Dans la boîte de dialogue **Identité AAD de cluster**, sous **Sélectionner un principal de service AD**, sélectionnez **Créer**.

Après avoir nommé le principal du service et avoir créé un mot de passe pour celui-ci, cliquez sur **Gérer l'accès ADLS** pour associer le principal du service à vos magasins Data Lake.

Vous pouvez également permettre au cluster d'accéder à un ou plusieurs magasins Data Lake après la création de ce cluster en ouvrant l’entrée Portail Azure d'un magasin Data Lake puis en accédant au menu **Explorateur de données > Accès > Ajouter**. 

Pour plus d’informations sur l'ajout d’un accès aux magasins Data Lake par un cluster HDI, consultez l’article [Créer un cluster HDInsight avec Data Lake Store à l'aide du portail Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#create-an-hdinsight-cluster-with-access-to-azure-data-lake-store)

## <a name="use-the-data-lake-store-with-r-server"></a>Utiliser le magasin Data Lake avec R Server
Après avoir créé un accès au magasin Data Lake, vous pouvez utiliser le magasin dans R Server sur HDInsight de la même façon qu’un compte de stockage Azure secondaire. La seule différence est que le préfixe **wasb://** devient **adl://**, comme suit :

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Voici les commandes utilisées pour configurer le compte de stockage Data Lake avec le répertoire RevoShare et pour ajouter l’exemple de fichier .csv de l’exemple précédent :

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## <a name="use-azure-files-on-the-edge-node"></a>Utiliser des fichiers Azure sur le nœud de périmètre
Il existe également une option de stockage de données pratique pour une utilisation sur le nœud de périmètre appelée [Fichiers Azure](../storage/storage-how-to-use-files-linux.md "Fichiers Azure"). Elle vous permet de monter un partage de fichiers Azure Storage sur le système de fichiers Linux. Cela peut être pratique pour stocker des fichiers de données, des scripts R et des objets de résultats qui peuvent être nécessaires ultérieurement, lorsqu’il sera judicieux d’utiliser le système de fichiers natif sur le nœud de périmètre plutôt que HDFS.

Le principal avantage des fichiers Azure est que les partages de fichiers peuvent être montés et utilisés par tout système disposant d’un système d’exploitation pris en charge, tel que Windows ou Linux. Par exemple, ils peuvent être utilisés par un autre cluster HDInsight que vous ou un membre de votre équipe avez, par une machine virtuelle Azure ou même par un système local.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous maîtrisez les options de stockage Azure, utilisez les liens suivants pour découvrir d’autres façons de travailler avec R Server sur HDInsight.

* [Vue d’ensemble : R Server sur HDInsight (version préliminaire)](hdinsight-hadoop-r-server-overview.md)
* [Prise en main de R Server sur Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Ajouter RStudio Server à HDInsight (s’il n’est pas ajouté lors de la création du cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](hdinsight-hadoop-r-server-compute-contexts.md)


