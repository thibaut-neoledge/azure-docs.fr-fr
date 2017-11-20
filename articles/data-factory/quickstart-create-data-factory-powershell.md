---
title: "Créer une fabrique de données Azure à l’aide de PowerShell | Microsoft Docs"
description: "Créez une fabrique de données Azure pour copier les données d’un emplacement dans un stockage Blob Azure vers un autre emplacement du même stockage Blob."
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 63e4c654409651f6655da1bed6ab2f544cf024dd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-azure-data-factory-and-pipeline-using-powershell"></a>Créer une fabrique de données Azure et un pipeline à l’aide de PowerShell
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-powershell.md)

Ce guide de démarrage rapide explique comment utiliser PowerShell pour créer une fabrique de données Azure. Le pipeline créé dans cette fabrique de données copie les données d’un emplacement vers un autre emplacement dans un stockage Blob Azure. Pour un didacticiel sur la transformation des données à l’aide d’Azure Data Factory, consultez l’article [Didacticiel : transformation des données à l’aide de Spark](transform-data-using-spark.md). 

Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour une présentation du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Composants requis

### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-storage-account"></a>Compte Stockage Azure
Dans ce guide de démarrage rapide, vous utilisez un compte Stockage Azure à usage général (stockage d’objets Blob spécifiquement) à la fois comme banque de données **source** et **réceptrice/de destination**. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour en créer un. 

#### <a name="get-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce guide de démarrage rapide, vous spécifiez le nom et la clé de votre compte Stockage Azure. La procédure suivante détaille les étapes à suivre pour obtenir le nom et la clé de votre compte de stockage. 

1. Lancez un navigateur web et accédez au [portail Azure](https://portal.azure.com). Connectez-vous en utilisant un nom d’utilisateur et un mot de passe Azure. 
2. Cliquez sur **Plus de services >** dans le menu de gauche, filtrez en utilisant le mot clé **Stockage**, puis sélectionnez **comptes de stockage**.

    ![Recherche du compte de stockage](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire), puis sélectionnez **votre compte de stockage**. 
4. Dans la page **compte de stockage**, sélectionnez **Clés d’accès** dans le menu.

    ![Obtenir le nom et la clé du compte de stockage](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Copiez les valeurs des champs **Nom du compte de stockage** et **key1** dans le presse-papiers. Collez-les dans un bloc-notes ou tout autre éditeur et enregistrez-le.  

#### <a name="create-input-folder-and-files"></a>Créer les dossiers et les fichiers d’entrée
Dans cette section, vous allez créer un conteneur d’objets blob nommé adftutorial dans le stockage Blob Azure. Ensuite, vous allez créer un dossier nommé entrée dans le conteneur et charger un exemple de fichier dans le dossier d’entrée. 

1. Installez l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) si vous ne l’avez pas sur votre ordinateur. 
2. Lancez l’**Explorateur Stockage Microsoft Azure** sur votre ordinateur.   
3. Dans la fenêtre **Se connecter au Stockage Azure**, sélectionnez **Utiliser un nom et une clé de compte de stockage**, puis cliquez sur **Suivant**. Si vous ne voyez pas la fenêtre **Se connecter au stockage Azure**, cliquez avec le bouton droit sur **Comptes de stockage** dans l’arborescence, puis cliquez sur **Se connecter au stockage Azure**. 

    ![Se connecter au Stockage Azure](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. Dans la fenêtre **Attacher à l’aide du nom et de la clé**, collez le **nom de compte** et la **clé de compte** que vous avez enregistrés à l’étape précédente. Cliquez ensuite sur **Suivant**. 
5. Dans la fenêtre **Résumé de la connexion**, cliquez sur **Se connecter**.
6. Vérifiez que votre compte de stockage s’affiche dans l’arborescence sous **(local et attaché)** -> **Comptes de stockage**. 
7. Développez **Conteneurs d’objets blob** et vérifiez que le conteneur d’objets blob **adftutorial** n’existe pas. S’il existe déjà, ignorez les étapes suivantes pour créer le conteneur. 
8. Cliquez avec le bouton droit sur **Conteneurs d’objets blob** puis, sélectionnez **Créer un conteneur d’objets blob**.

    ![Création du conteneur d’objets blob](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. Saisissez **adftutorial** pour le nom et appuyez sur **ENTRÉE**. 
10. Vérifiez que le conteneur **adftutorial** est sélectionné dans l’arborescence. 
11. Cliquez sur **Nouveau dossier** dans la barre d’outils. 

    ![Créer un bouton de dossier](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. Dans la fenêtre **Créer un nouveau répertoire virtuel**, saisissez **entrée** dans le champ **Nom**, puis cliquez sur **OK**. 

    ![Créer une boîte de dialogue](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. Lancez le **Bloc-notes** et créez un fichier nommé **emp.txt** avec le contenu suivant : 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    Enregistrez-le dans le dossier **c:\ADFv2QuickStartPSH** ; créez le dossier **ADFv2QuickStartPSH** si il n’existe pas déjà. 
14. Cliquez sur le bouton **Télécharger** sur la barre d’outils, puis sélectionnez **Télécharger des fichiers**. 

    ![Bouton Télécharger](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. Dans la fenêtre **Charger des fichiers**, pour **Fichiers**, sélectionnez `...`. 
16. Dans la fenêtre **Sélectionner le dossier à charger**, accédez au dossier contenant le fichier **emp.txt**, puis sélectionnez-le. 

    ![Boîte de dialogue charger des fichiers](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. Dans la fenêtre **Charger des fichiers**, cliquez sur **Charger**. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Installation d'Azure PowerShell
Installez la dernière version de Azure PowerShell, si elle n’est pas installée sur votre ordinateur. 

1. Dans votre navigateur web, accédez à la page [Téléchargements du Kit de développement logiciel Azure et kits de développement logiciel](https://azure.microsoft.com/downloads/). 
2. Cliquez sur **Installation Windows** dans la section **Outils de ligne de commande** -> **PowerShell**. 
3. Pour installer Azure PowerShell, exécutez le fichier **MSI**. 

Pour des instructions détaillées, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Connexion à Azure PowerShell
Lancez **PowerShell** sur votre ordinateur. Gardez Azure PowerShell ouvert jusqu’à la fin de ce guide de démarrage rapide. Si vous la fermez, puis la rouvrez, vous devez réexécuter ces commandes.

1. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe Azure que vous utilisez pour la connexion au portail Azure :
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Si vous avez plusieurs abonnements Azure, exécutez la commande suivante pour afficher tous les abonnements de ce compte :

    ```powershell
    Get-AzureRmSubscription
    ```
3. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **SubscriptionId** par l’ID de votre abonnement Azure :

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données
1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez dans les commandes PowerShell plus tard. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Définissez une variable pour le nom de la fabrique de données que vous pourrez utiliser dans les commandes PowerShell plus tard. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Définissez une variable pour l’emplacement de la fabrique de données : 

    ```powershell
    $location = "East US"
    ```
4. Pour créer le groupe de ressources Azure, utilisez la commande suivante : 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$resourceGroupName` et essayez de nouveau. Si vous souhaitez partager le groupe de ressources, passez à l’étape suivante. 
5. Pour créer une fabrique de données, exécutez l’applet de commande suivante **Set-AzureRmDataFactoryV2** : 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Notez les points suivants :

* Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Pour créer des instances Data Factory, vous devez être un **collaborateur** ou un **administrateur** de l’abonnement Azure.
* À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

## <a name="create-a-linked-service"></a>Créer un service lié

Créez des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce guide de démarrage rapide, vous devez uniquement créer un service lié Azure Storage à utiliser à la fois comme magasins source et récepteur, nommé « AzureStorageLinkedService » dans cet exemple.

1. Créez un fichier JSON nommé **AzureStorageLinkedService.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant : (créez le dossier ADFv2QuickStartPSH s’il n’existe pas déjà). 

    > [!IMPORTANT]
    > Remplacez &lt;accountName&gt; et &lt;accountKey&gt; par le nom et la clé de votre compte de stockage Azure avant d’enregistrer le fichier.

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

Vous définissez un jeu de données qui représente les données à copier d’une source vers un récepteur. Dans cet exemple, ce jeu de données d’objet blob fait référence au service lié Azure Storage que vous avez créé à l’étape précédente. Le jeu de données prend un paramètre dont la valeur est définie dans une activité qui consomme le jeu de données. Le paramètre est utilisé pour construire le **FolderPath** pointant vers l’emplacement où les données résident/sont stockées.

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
  
Dans cet exemple, ce pipeline contient une activité et accepte deux paramètres : chemin de l’objet blob d’entrée et chemin de l’objet blob de sortie. Les valeurs de ces paramètres sont définies quand le pipeline est déclenché/exécuté. L’activité de copie utilise le même jeu de données d’objet blob créé à l’étape précédente comme entrée et sortie. Quand le jeu de données est utilisé comme jeu de données d’entrée, le chemin d’entrée est spécifié. De même, quand le jeu de données est utilisé comme jeu de données de sortie, le chemin de sortie est spécifié. 

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

    Remplacez la valeur des paramètres **inputPath** et **outputPath** par les chemins des objets blob source et récepteur si vous utilisez différents conteneurs et dossiers.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
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
Le pipeline crée automatiquement le dossier de sortie dans le conteneur d’objets blob adftutorial. Ensuite, il copie le fichier emp.txt à partir du dossier d’entrée dans le dossier de sortie. Utilisez l’[explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que les objets blob dans inputBlobPath sont copiés dans outputBlobPath. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous disposez de deux moyens de supprimer les ressources que vous avez créées dans le guide de démarrage rapide. Vous pouvez supprimer le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) qui inclut toutes les ressources du groupe de ressources. Si vous souhaitez conserver les autres ressources, supprimez uniquement la fabrique de données créée dans ce didacticiel.

Exécutez la commande suivante pour supprimer l’intégralité du groupe de ressources : 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Exécutez la commande suivante pour supprimer uniquement la fabrique de données : 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 
