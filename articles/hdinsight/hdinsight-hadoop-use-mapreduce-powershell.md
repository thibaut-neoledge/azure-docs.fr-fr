---
title: Utilisation de MapReduce et PowerShell avec Hadoop | Microsoft Docs
description: "Apprenez à utiliser PowerShell pour exécuter des tâches MapReduce à distance avec Hadoop sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 9eafaca75bbf3db639a42c8206d2ac506c255273
ms.lasthandoff: 03/22/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Exécution à distance des travaux MapReduce avec Hadoop sur HDInsight à l’aide de PowerShell

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour exécuter une tâche MapReduce sur un Hadoop sur un cluster HDInsight.

## <a id="prereq"></a>Configuration requise

* **Un cluster Azure HDInsight (Hadoop sur HDInsight)**

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **Un poste de travail sur lequel est installé Azure PowerShell**.

## <a id="powershell"></a>Exécution d’une tâche MapReduce avec Azure PowerShell

Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches MapReduce sur HDInsight. En interne, cela est accompli en effectuant des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches MapReduce sur un cluster HDInsight à distance.

* **Login-AzureRmAccount** : authentifie Azure PowerShell sur votre abonnement Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition** : crée une *définition de tâche* avec les informations MapReduce spécifiées.

* **Start-AzureRmHDInsightJob** : envoie la définition de la tâche à HDInsight, lance la tâche et retourne un objet de *tâche* pouvant être utilisé pour vérifier l’état de la tâche.

* **Wait-AzureRmHDInsightJob**: utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.

* **Get-AzureRmHDInsightJobOutput** : permet de récupérer la sortie de la tâche.

Les étapes suivantes montrent comment utiliser ces applets de commande pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant sous **mapreducejob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Ouvrez une invite de commandes **Azure PowerShell** . Accédez à l’emplacement du fichier **mapreducejob.ps1** , puis utilisez les éléments suivants pour exécuter le script :
   
        .\mapreducejob.ps1
   
    Lorsque vous exécutez le script, vous êtes invité à entrer le nom du cluster HDInsight et le nom du compte HTTPS/Admin, ainsi que le mot de passe pour le cluster. Vous pouvez également être invité à vous authentifier sur votre abonnement Azure.

3. Une fois la tâche terminée, vous obtenez un résultat similaire au texte suivant :
    
        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071
    
    Cela indique que la tâche a été effectuée avec succès.
    
    > [!NOTE]
    > Si **ExitCode** correspond à une valeur différente de 0, consultez [Dépannage](#troubleshooting).
    
    Cet exemple stocke également les fichiers téléchargés dans un dossier **output.txt** dans le répertoire à partir duquel vous avez exécuté le script.

### <a name="view-output"></a>Affichage de la sortie

Ouvrez le fichier **output.txt** dans un éditeur de texte pour afficher les mots et les décomptes générés par la tâche.

> [!NOTE]
> Les fichiers de résultat d’une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devez modifier le nom du fichier de sortie.

## <a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n'est retournée lorsque la tâche est terminée, il se peut qu'une erreur soit survenue au cours du traitement. Pour afficher les informations d’erreur pour ce projet, ajoutez la commande suivante à la fin du fichier **mapreducejob.ps1** , enregistrez-le et exécutez-le à nouveau.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Cette applet de commande renvoie les informations écrites dans STDERR sur le serveur lors de l’exécution de la tâche et peut vous aider à déterminer pourquoi la tâche échoue.

## <a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell offre un moyen facile d’exécuter des tâches MapReduce sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)


