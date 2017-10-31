---
title: "Créer une fabrique de données Azure à l’aide de PowerShell | Microsoft Docs"
description: "Créez une fabrique de données Azure pour copier les données d’un emplacement dans un stockage Blob Azure vers un autre emplacement dans le même stockage Blob."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: efcdcac0edcdc3e3bd87dae89609e04985a3579e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2017
---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>Créer une fabrique de données et un pipeline à l’aide de PowerShell
Azure Data Factory est un service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement et la transformation des données. Grâce à Azure Data Factory, vous pouvez créer et planifier des flux de travail orientés données (appelés pipelines) capables d’ingérer des données provenant de différents magasins de données, de traiter/transformer les données à l’aide de services de calcul comme Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning, et de publier des données de sortie dans des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser. 

Ce guide de démarrage rapide explique comment utiliser PowerShell pour créer une fabrique de données Azure. Le pipeline dans cette fabrique de données copie les données d’un emplacement vers un autre emplacement dans un stockage Blob Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

* **Compte Stockage Azure**. Vous utilisez le stockage Blob à la fois comme magasins de données **source** et **récepteur**. Si vous ne possédez pas de compte de stockage Azure, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour en créer un. 
* Créez un **conteneur d’objets blob** dans le stockage Blob, créez un **dossier** d’entrée dans le conteneur et chargez des fichiers sur le dossier. Vous pouvez utiliser des outils tels que l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vous connecter au stockage Blob Azure, créer un conteneur d’objets blob, charger le fichier d’entrée et vérifier le fichier de sortie.
* **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez **PowerShell**. Gardez Azure PowerShell ouvert jusqu’à la fin de ce guide de démarrage rapide. Si vous fermez puis rouvrez Azure PowerShell, vous devez réexécuter ces commandes.

    Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure :
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Exécutez la commande suivante pour afficher tous les abonnements de ce compte :

    ```powershell
    Get-AzureRmSubscription
    ```
    Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **SubscriptionId** par l’ID de votre abonnement Azure :

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2** pour créer une fabrique de données. Remplacez les espaces réservés par vos propres valeurs avant d’exécuter la commande. **Espaces réservés** remplacés par vos propres valeurs. 

    Définissez une variable pour le nom du groupe de ressources que vous pourrez utiliser dans les commandes PowerShell plus tard. 
    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    ```

    Définissez une variable pour le nom de la fabrique de données que vous pourrez utiliser dans les commandes PowerShell plus tard. 

    ```powershell
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    ```

    Exécutez la commande suivante pour créer la fabrique de données. 
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

    Notez les points suivants :

    * Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Pour créer des instances de fabrique de données, vous devez avoir le statut d’administrateur/collaborateur de l’abonnement Azure.
    * À l’heure actuelle, Data Factory version 2 vous permet de créer une fabrique de données uniquement dans la région **Est des États-Unis** ou **Est des États-Unis 2**. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

## <a name="create-a-linked-service"></a>Créer un service lié

Créez des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce guide de démarrage rapide, vous devez uniquement créer un service lié Azure Storage à utiliser à la fois comme magasins source et récepteur, nommé « AzureStorageLinkedService » dans cet exemple.

1. Créez un fichier JSON nommé **AzureStorageLinkedService.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant : (créez le dossier ADFv2QuickStartPSH s’il n’existe pas déjà). Remplacez &lt;accountName&gt; et &lt;accountKey&gt; par le nom et la clé de votre compte de stockage Azure avant d’enregistrer le fichier.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. Dans **Azure PowerShell**, basculez vers le dossier **ADFv2QuickStartPSH**.

3. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2LinkedService** pour créer le service lié **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Créer un jeu de données

Vous définissez un jeu de données qui représente les données à copier d’une source vers un récepteur. Dans cet exemple, ce jeu de données d’objet blob fait référence au service lié Azure Storage que vous avez créé à l’étape précédente. Le jeu de données prend un paramètre dont la valeur est définie dans une activité qui consomme le jeu de données. Le paramètre est utilisé pour construire le « FolderPath » pointant vers l’emplacement où les données résident/sont stockées.

1. Créez un fichier JSON nommé **BlobDataset.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant :

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Pour créer le jeu de données **BlobDataset**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Voici l'exemple de sortie :

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Créer un pipeline
  
Dans cet exemple, ce pipeline contient une activité et accepte deux paramètres : chemin de l’objet blob d’entrée et chemin de l’objet blob de sortie. Les valeurs de ces paramètres sont définies quand le pipeline est déclenché/exécuté. L’activité de copie fait référence au même jeu de données d’objet blob créé à l’étape précédente comme entrée et sortie. Quand le jeu de données est utilisé comme jeu de données d’entrée, le chemin d’entrée est spécifié. De même, quand le jeu de données est utilisé comme jeu de données de sortie, le chemin de sortie est spécifié. 

1. Créez un fichier JSON nommé **Adfv2QuickStartPipeline.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant :

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Pour créer le pipeline **Adfv2QuickStartPipeline**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Voici l'exemple de sortie :

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline

Dans cette étape, vous définissez des valeurs pour les paramètres de pipeline : **inputPath** et **outputPath** avec les valeurs réelles des chemins des objets blob source et récepteur. Vous créez ensuite une exécution du pipeline à l’aide de ces arguments. 

1. Créez un fichier JSON nommé **PipelineParameters.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant :

    Remplacez la valeur des paramètres « inputPath » et « outputPath » par les chemins des objets blob source et récepteur pour copier les données entrantes et sortantes avant d’enregistrer le fichier.

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. Exécutez l’applet de commande **Invoke-AzureRmDataFactoryV2Pipeline** pour créer une exécution du pipeline et passer les valeurs des paramètres. Cette opération capture également l’ID d’exécution du pipeline pour une surveillance ultérieure.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Surveiller une exécution du pipeline

1. Exécutez le script suivant afin de vérifier en permanence l’état de l’exécution du pipeline jusqu’à la fin de la copie des données.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    Voici l’exemple de sortie de l’exécution du pipeline :

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. Exécutez le script suivant pour récupérer les détails de l’exécution de l’activité de copie, par exemple la taille des données lues/écrites.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Vérifiez que la sortie est similaire à l’exemple de sortie suivant pour le résultat de l’exécution de l’activité :

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Vérifier la sortie
Utilisez des outils comme l’[explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que les objets blob dans inputBlobPath sont copiés dans outputBlobPath.

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous disposez de deux moyens de supprimer les ressources que vous avez créées dans le guide de démarrage rapide. Vous pouvez supprimer le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) qui inclut toutes les ressources du groupe de ressources. Si vous souhaitez conserver les autres ressources, supprimez uniquement la fabrique de données créée dans ce didacticiel.

Exécutez la commande suivante pour supprimer l’intégralité du groupe de ressources : 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Exécutez la commande suivante pour supprimer uniquement la fabrique de données : 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 
