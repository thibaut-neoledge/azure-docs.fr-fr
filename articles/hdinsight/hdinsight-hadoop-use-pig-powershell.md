---
title: Utilisation de Hadoop Pig avec PowerShell dans HDInsight | Microsoft Docs
description: Découvrez comment soumettre des tâches Pig vers un cluster Hadoop sur HDInsight à l’aide d’Azure Powershell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="run-pig-jobs-using-powershell"></a>Exécution de tâches Pig avec PowerShell
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour soumettre des tâches Pig à un Hadoop sur un cluster HDInsight. Pig vous permet d’écrire des tâches MapReduce en utilisant un langage (Pig Latin) qui modélise les transformations de données, plutôt que de mapper et de réduire les fonctions.

> [!NOTE]
> Ce document ne fournit pas une description détaillée de ce que font les instructions Pig Latin utilisées dans les exemples. Pour plus d’informations sur le langage Pig Latin utilisé dans cet exemple, consultez la rubrique [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md).
> 
> 

## <a name="<a-id="prereq"></a>prerequisites"></a><a id="prereq"></a>Configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un poste de travail sur lequel est installé Azure PowerShell**.
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="<a-id="powershell"></a>run-pig-jobs-using-powershell"></a><a id="powershell"></a>Exécution de tâches Pig avec PowerShell
Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches Pig sur HDInsight. En interne, cela est accompli en effectuant des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches Pig sur un cluster HDInsight à distance :

* **Login-AzureRmAccount**: authentifie Azure PowerShell sur votre abonnement Azure.
* **New-AzureRmHDInsightPigJobDefinition**: crée une *définition d’une tâche* à l’aide des instructions Pig Latin spécifiées.
* **Start-AzureRmHDInsightJob**: envoie la définition de la tâche à HDInsight, démarre la tâche et retourne un objet de *tâche* pouvant être utilisé pour vérifier le statut de la tâche.
* **Wait-AzureRmHDInsightJob**: utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.
* **Get-AzureRmHDInsightJobOutput**: utilisé pour récupérer la sortie de la tâche.

Les étapes suivantes montrent comment utiliser ces cmdlets pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant sous **pigjob.ps1**. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.
   
        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential
   
        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
   
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
   
        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"

        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

1. Ouvrez une invite de commandes Windows PowerShell. Accédez au répertoire du fichier **pigjob.ps1** , puis utilisez la commande suivante pour exécuter le script :
   
        .\pigjob.ps1
   
    Vous êtes d’abord invité à vous connecter à votre abonnement Azure. Ensuite, vous devez fournir le nom et le mot de passe du compte HTTPs/Admin pour le cluster HDInsight.
2. Une fois la tâche terminée, des informations similaires à celles présentées ci-dessous doivent s’afficher :
   
        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a name="<a-id="troubleshooting"></a>troubleshooting"></a><a id="troubleshooting"></a>Résolution des problèmes
Si aucune information n'est retournée lorsque la tâche est terminée, il se peut qu'une erreur soit survenue au cours du traitement. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **pigjob.ps1** , enregistrez-le et exécutez-le à nouveau.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Cela renvoie les informations écrites dans STDERR sur le serveur lors de l’exécution de la tâche et peut vous aider à déterminer pourquoi la tâche échoue.

## <a name="<a-id="summary"></a>summary"></a><a id="summary"></a>Résumé
Comme vous pouvez le voir, Azure PowerShell offre un moyen facile d’exécuter des tâches Pig sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

## <a name="<a-id="nextsteps"></a>next-steps"></a><a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=Oct16_HO2-->


