---
title: "Utilisation de Hadoop Hive avec PowerShell dans HDInsight | Microsoft Docs"
description: "Utilisez PowerShell pour exécuter des requêtes Hive dans Hadoop sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: bb5fee05bb14276ab0a9e2309f8a20cb3684df57


---
# <a name="run-hive-queries-using-powershell"></a>Exécution de requêtes Hive avec PowerShell
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell dans le mode Groupe de ressources Azure pour exécuter des requêtes Hive sur un cluster Hadoop sur HDInsight.

> [!NOTE]
> Ce document ne fournit pas de description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d’informations sur le langage HiveQL utilisé dans cet exemple, consultez la page [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md).
> 
> 

**Configuration requise**

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* **Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux**
* **Un poste de travail sur lequel est installé Azure PowerShell**.
  
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Exécution de requêtes Hive avec Azure PowerShell
Azure PowerShell fournit des *cmdlets* qui vous permettent d'exécuter à distance des requêtes Hive sur HDInsight. En interne, cela est accompli en effectuant des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivants sont utilisés lors de l'exécution de requêtes Hive sur un cluster à distance HDInsight :

* **Add-AzureRmAccount**: authentifie Azure PowerShell dans votre abonnement Azure.
* **New-AzureRmHDInsightHiveJobDefinition**: crée une *définition de travail* à l’aide des instructions HiveQL spécifiées.
* **Start-AzureRmHDInsightJob**: envoie la définition de la tâche à HDInsight, démarre la tâche et retourne un objet de *tâche* pouvant être utilisé pour vérifier le statut de la tâche.
* **Wait-AzureRmHDInsightJob**: utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.
* **Get-AzureRmHDInsightJobOutput**: utilisé pour récupérer la sortie de la tâche.
* **Invoke-AzureRmHDInsightHiveJob**: utilisé pour exécuter des instructions HiveQL. Cela bloque la fin de la requête, puis retourne les résultats.
* **Use-AzureRmHDInsightCluster** : configure le cluster actuel à utiliser pour la commande **Invoke-AzureRmHDInsightHiveJob**

Les étapes suivantes montrent comment utiliser ces cmdlets pour exécuter une tâche sur votre cluster HDInsight :

1. À l'aide d'un éditeur, enregistrez le code suivant en tant que **hivejob.ps1**. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.
   
        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential
   
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }
   
        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
   
        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 
   
        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green
   
        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds
   
        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds
   
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
2. Ouvrez une invite de commandes **Azure PowerShell** . Accédez au répertoire du fichier **hivejob.ps1** , puis utilisez la commande suivante pour exécuter le script :
   
        .\hivejob.ps1
   
    Quand le script s’exécute, vous devez entrer les informations d’identification du compte HTTPS/Admin pour votre cluster. Vous pouvez également être invité à vous connecter à votre abonnement Azure.
3. Une fois la tâche terminée, des informations similaires à celles présentées ci-dessous doivent s’afficher :
   
        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
4. Comme mentionné précédemment, **Invoke-Hive** peut être utilisé pour exécuter une requête et attendre la réponse. Utilisez les commandes suivantes, en remplaçant **CLUSTERNAME** par le nom de votre cluster :
   
        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString
   
    La sortie se présente comme suit :
   
        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
   
   > [!NOTE]
   > Pour les requêtes HiveQL plus longues, vous pouvez utiliser les fichiers de script HiveQL de PowerShell ou la cmdlet **Here-Strings** Azure PowerShell. L'extrait suivant montre comment utiliser la cmdlet **Invoke-Hive** pour exécuter un fichier de script HiveQL. Ce dernier doit être téléchargé vers wasbs://.
   > 
   > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   > 
   > Pour plus d'informations sur **Here-Strings**, consultez la page <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Utilisation du fichier de script Here-Strings de PowerShell</a>.
   > 
   > 

## <a name="troubleshooting"></a>Résolution des problèmes
Si aucune information n'est retournée lorsque la tâche est terminée, il se peut qu'une erreur soit survenue au cours du traitement. Pour afficher les informations d'erreur relatives à cette tâche, ajoutez les commandes suivantes à la fin du fichier **hivejob.ps1** , enregistrez-le, puis exécutez-le à nouveau.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Cela renvoie les informations écrites dans STDERR sur le serveur lors de l’exécution du travail et peut vous aider à déterminer pourquoi la tâche échoue.

## <a name="summary"></a>Résumé
Comme vous pouvez le constater, Azure PowerShell permet d'exécuter facilement des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Nov16_HO3-->


