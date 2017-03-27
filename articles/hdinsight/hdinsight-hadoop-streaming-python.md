---
title: "Développement de travaux MapReduce Python avec HDInsight | Microsoft Docs"
description: "Découvrez comment créer et exécuter des tâches MapReduce Python sur des clusters HDInsight Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cf233279c0a26c3d2970596b1bb515508da20b89
ms.openlocfilehash: ad696f14d48452840805bc413d890309e523ce34
ms.lasthandoff: 02/07/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>Développement de programmes de diffusion en continu Python pour HDInsight

Hadoop fournit une API de diffusion en continu pour MapReduce qui vous permet d'écrire des fonctions de mappage et de réduction dans d'autres langages que Java. Cet article explique comment utiliser Python pour effectuer des opérations MapReduce.

Cet article est basé sur des informations et des exemples publiés par Michael Noll dans le didacticiel [Writing an Hadoop MapReduce Program in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop Linux sur HDInsight

  > [!IMPORTANT]
  > Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un éditeur de texte
  
  > [!IMPORTANT]
  > L’éditeur de texte doit utiliser LF comme caractère de fin de ligne. S’il utilise CRLF, des erreurs se produisent pendant l’exécution du travail MapReduce sur des clusters HDInsight basés sur Linux. En cas de doute, utilisez l’étape facultative dans la section [Exécuter MapReduce](#run-mapreduce) pour convertir tout caractère CRLF en caractère LF.

* **Des connaissances en SSH et SCP**. Pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez les articles suivants :
  
  * **Clients Linux, Unix ou OS X**: consultez la page [Utilisation de SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

  * **Clients Windows**: consultez [Utilisation de SSH avec un cluster Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="word-count"></a>Nombre de mots

Dans cet exemple, vous allez implémenter un nombre de mots de base à l’aide d’un mappeur et d’un raccord de réduction. Le mappeur sépare les phrases en mots individuels, et le raccord de réduction rassemble les mots et les nombres pour produire la sortie.

L’organigramme suivant illustre ce qui se passe durant les phases de mappage et de réduction.

![illustration du mappage de réduction](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>Pourquoi Python

Python est un langage de programmation d'usage général et de haut niveau, qui, comparé à de nombreux autres langages, vous permet d'exprimer des concepts avec beaucoup moins de lignes de code. Des données scientifiques l’ont récemment fait connaître en tant que langage de prototypage, du fait de sa nature interprétée, sa saisie dynamique et sa syntaxe élégante qui lui permettent de développer très rapidement des applications.

Python est installé sur tous les clusters HDInsight.

## <a name="streaming-mapreduce"></a>Diffusion en continu de MapReduce

Hadoop vous permet de spécifier un fichier qui dispose de la logique de mappage et de réduction utilisée par un travail. Parmi les exigences spécifiques de mappage et de réduction, on retrouve les éléments suivants :

* **Entrée**: les composants de mappage et de réduction doivent lire les données d’entrée depuis STDIN.
* **Sortie**: les composants de mappage et de réduction doivent écrire les données de sortie vers STDOUT.
* **Format de données**: les données consommées et produites doivent représenter une paire clé/valeur, séparée par un caractère de tabulation.

Python peut facilement gérer ces exigences en utilisant le module **sys** pour lire depuis STDIN et **print** pour imprimer vers STDOUT. Le travail restant consiste à disposer un caractère de tabulation (`\t`) entre la clé et la valeur pour vous permettre d’effectuer, si vous le souhaitez, le formatage de ces données.

## <a name="create-the-mapper-and-reducer"></a>Création du mappeur et du raccord de réduction

Le mappeur et le raccord de réduction ne sont que des fichiers texte, ici **mapper.py** et **reducer.py**. De ce fait, nous savons exactement qui fait quoi. Vous pouvez les créer à l’aide de l’éditeur de votre choix.

### <a name="mapperpy"></a>Mapper.py

Créez un fichier nommé **mapper.py** et utilisez le code suivant comme contenu :

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

Prenez quelques instants pour lire le code et comprendre à quoi il sert.

### <a name="reducerpy"></a>Reducer.py

Créez un fichier nommé **reducer.py** et utilisez le code suivant comme contenu :

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>Téléchargement des fichiers

Les deux fichiers **mapper.py** et **reducer.py** doivent se trouver sur le nœud principal du cluster pour pouvoir être exécutés. Cette section fournit un exemple de commande `scp` et un script Azure PowerShell qui peut être utilisé pour télécharger les fichiers sur le cluster.

> [!IMPORTANT]
> Il existe une différence importante entre l’utilisation de `scp` et de PowerShell pour télécharger les fichiers :
>
> * Si vous utilisez `scp`, les fichiers sont placés sur le nœud principal du cluster. Cela suppose que vous vous connecterez ultérieurement au nœud principal et que vous exécuterez le travail à partir d’une session SSH.
> * Si vous utilisez le script PowerShell, les fichiers sont placés dans le stockage par défaut du cluster. Cela suppose que vous utiliserez ultérieurement le script PowerShell pour exécuter le travail à partir d’un client distant.

### <a name="upload-using-scp"></a>Téléchargement à l’aide de SCP

À partir de votre environnement de développement, dans le même répertoire que **mapper.py** et **reducer.py**, utilisez la commande suivante. Remplacez **username** par le nom d’utilisateur SSH de votre cluster, et remplacez **clustername** par le nom de votre cluster.

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

De cette façon, les fichiers du système local sont copiés dans le nœud principal.

> [!NOTE]
> Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d’accès à la clé privée, par exemple, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

### <a name="upload-using-powershell"></a>Téléchargement à l’aide de PowerShell

1. À partir de votre environnement de développement, créez un fichier nommé `Put-FilesInHDInsight.ps1` et utilisez le contenu suivant :

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Pour utiliser ce script pour charger un fichier, utilisez la commande suivante à partir d’une invite Azure PowerShell :

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    Lorsque vous y êtes invité, saisissez les informations de connexion HTTPS de votre cluster.

3. Répétez la commande, en remplaçant `mapper.py` par `reducer.py`. Cela chargera les fichiers du mappeur et du raccord de réduction vers le stockage par défaut pour le cluster.

## <a name="run-mapreduce-ssh"></a>Exécution de MapReduce (SSH)

Suivez les étapes ci-dessous pour vous connecter au cluster et exécuter le travail de diffusion en continu MapReduce à partir d’une session SSH.

1. Connectez-vous au cluster à l’aide de SSH :
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d’accès à la clé privée, par exemple, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Facultatif) Si vous avez employé un éditeur de texte qui utilise CRLF comme caractère de fin de ligne pendant la création des fichiers mapper.py et reducer.py, ou que vous ignorez le caractère de fin de ligne utilisé par votre éditeur, recourez aux commandes suivantes pour convertir en LF les occurrences de CRLF dans les fichiers mapper.py et reducer.py.
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. Exécutez la commande suivante pour démarrer la tâche MapReduce :
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    Cette commande dispose des éléments suivants :
   
   * **hadoop-streaming.jar**: utilisé lors de l’exécution d’opérations de diffusion en contenu MapReduce. Il établit un lien entre Hadoop et le code externe MapReduce que vous fournissez

   * **-files**: indique à Hadoop que les fichiers spécifiés sont nécessaires pour effectuer cette tâche MapReduce, et qu’ils doivent être copiés sur tous les nœuds de travail.

   * **-mapper**: indique à Hadoop quel fichier doit être utilisé comme mappeur.

   * **-reducer**: indique à Hadoop quel fichier doit être utilisé comme raccord de réduction.

   * **-input**: le fichier d’entrée à partir duquel nous devrions compter les mots.

   * **-output**: le répertoire sur lequel la sortie sera écrite
     
     > [!NOTE]
     > Ce répertoire sera créé par la tâche.

Vous devriez voir apparaître des instructions **INFO** à mesure que la tâche s’exécute, et des opérations de **mappage** et de **réduction** s’afficher sous forme de pourcentages.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Lorsque le travail se termine, vous recevez les informations sur son état.

## <a name="run-mapreduce-powershell"></a>Exécution de MapReduce (PowerShell)

Procédez comme suit pour exécuter le travail de diffusion continu MapReduce à partir de PowerShell dans votre environnement de développement. Le script PowerShell exécute le travail en se connectant au cluster HDInsight à l’aide de WebHCat.

1. Créez un fichier nommé `Start-HDInsightStreamingPythonJob` et utilisez le contenu suivant :

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Utilisez la commande suivante dans une invite Azure PowerShell pour exécuter le travail :

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    Cette opération utilise les fichiers `mapper.py` et `reducer.py` précédemment chargés sur le cluster pour compter les mots dans le fichier `davinci.txt`. Le résultat est stocké dans le dossier `/example/wordcount` sur le stockage du cluster par défaut.

    Une fois le travail terminé, des informations similaires à celles présentées ci-dessous s’affichent :

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>Affichage du résultat

Le résultat du travail est stocké dans le répertoire `/example/wordcountout`. Vous pouvez l’afficher à partir d’une session SSH, ou le télécharger localement et le consulter à partir de PowerShell.

Pour afficher les données à partir d’une session SSH vers le cluster, utilisez la commande suivante :

`hdfs dfs -text /example/wordcountout/part-00000`

Celle-ci énumère les mots qui sont utilisés, et elle vous indique, par la même occasion, le nombre de fois où ils apparaissent. Voici un exemple de données de sortie :

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

Pour afficher le résultat à partir de votre environnement de développement à l’aide de PowerShell, procédez comme suit :

1. Créez un fichier nommé `Get-FilesInHDInsight.ps1` et utilisez le contenu suivant :

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. À partir d’une invite Azure PowerShell, utilisez la commande suivante pour exécuter le script et afficher le résultat :

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    Celle-ci énumère les mots qui sont utilisés, et elle vous indique, par la même occasion, le nombre de fois où ils apparaissent. Voici un exemple de données de sortie :

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment utiliser des travaux de diffusion en continu MapReduce avec HDInsight, cliquez sur les liens suivants pour explorer d’autres façons d’utiliser Azure HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)


