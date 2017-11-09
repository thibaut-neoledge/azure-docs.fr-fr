---
title: "Copier des données locales dans le cloud en utilisant Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier les données d’un magasin de données local dans le cloud Azure en utilisant le runtime d’intégration auto-hébergé d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: jingwang
ms.openlocfilehash: 95d1dce536f8f8f0fc8d93f201520fd84f0f7629
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Copier des données entre un emplacement local et le cloud

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Ce didacticiel

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dans ce didacticiel, vous allez utiliser Azure PowerShell pour créer un pipeline Data Factory qui copie les données d’une base de données SQL Server locale dans un stockage Blob Azure. Vous créerez et utiliserez un runtime d’intégration (IR) auto-hébergé d’Azure Data Factory, qui permet l’intégration de magasins de données locaux et de magasins de données cloud.  Pour en savoir plus sur l’utilisation d’autres outils/SDK pour créer une fabrique de données, consultez [Guides de démarrage rapide](quickstart-create-data-factory-dot-net.md).

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un runtime d’intégration auto-hébergé.
> * Créer et chiffrer un service lié SQL Server local sur le runtime d’intégration auto-hébergé.
> * Créer un service lié Stockage Azure.
> * Créer des jeux de données SQL Server et Stockage Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller le pipeline et l’exécution d’activité.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

* **SQL Server**. Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données **source**.
* **Compte Stockage Azure**. Dans ce didacticiel, le stockage Blob Azure est utilisé comme magasin de données de **destination/réception**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
* **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez **PowerShell**. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous fermez puis rouvrez Azure PowerShell, vous devez réexécuter ces commandes.

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
2. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2** pour créer une fabrique de données. Avant d’exécuter la commande, remplacez les espaces réservés par vos propres valeurs.

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Notez les points suivants :

    * Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Pour créer des instances Data Factory, vous devez être collaborateur ou administrateur de l’abonnement Azure.
    * À l’heure actuelle, Data Factory vous permet de créer une fabrique de données uniquement dans les régions Est des États-Unis et Est des États-Unis 2. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

## <a name="create-a-self-hosted-ir"></a>Créer un runtime d’intégration auto-hébergé

Dans cette section, vous pouvez créer un runtime d’intégration auto-hébergé et l’associer à un nœud local (ordinateur).

1. Créez un runtime d’intégration auto-hébergé. Utilisez un nom unique au cas où il existe déjà un autre runtime d’intégration de même nom.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Voici l'exemple de sortie :

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Exécutez la commande suivante pour récupérer l’état du runtime d’intégration créé.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Voici l'exemple de sortie :

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Exécutez la commande suivante pour récupérer les clés d’authentification pour inscrire le runtime d’intégration auto-hébergé auprès du service Data Factory dans le cloud. Copiez l’une des clés pour inscrire le runtime d’intégration auto-hébergé.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Voici l'exemple de sortie :

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39717) le runtime d’intégration auto-hébergé sur un ordinateur Windows local et servez-vous de la clé d’authentification obtenue à l’étape précédente pour inscrire manuellement le runtime d’intégration auto-hébergé.

   ![Inscrire le runtime d’intégration](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Le message suivant s’affiche une fois que le runtime d’intégration auto-hébergé est bien inscrit :

   ![Inscription réussie](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   La page suivante apparaît une fois que le nœud est connecté au service cloud :

   ![Le nœud est connecté](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Créez des services liés

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Créer un service lié Stockage Azure (destination/réception)

1. Créez un fichier JSON sous le nom **AzureSqlDWLinkedService.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant. Créez le dossier ADFv2Tutorial s’il n’existe pas déjà.  Remplacez &lt;accountname&gt; et &lt;accountkey&gt; par le nom et la clé de votre compte de stockage Azure.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. Dans **Azure PowerShell**, accédez au dossier **ADFv2Tutorial**.

   Exécutez l’applet de commande **Set-AzureRmDataFactoryV2LinkedService** pour créer le service lié **AzureStorageLinkedService**. Les applets de commande utilisées dans ce didacticiel prennent des valeurs pour les paramètres **ResourceGroupName** et **DataFactoryName**. Vous pouvez aussi passer l’objet **DataFactory** retourné par l’applet de commande Set-AzureRmDataFactoryV2 sans avoir à taper ResourceGroupName et DataFactoryName chaque fois que vous exécutez une applet de commande.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Voici un exemple de sortie :

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Créer et chiffrer un service lié SQL Server (source)

1. Créez un fichier JSON sous le nom **SqlServerLinkedService.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant : avant d’enregistrer le fichier, remplacez **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** et **&lt;password>** par les valeurs de votre serveur SQL Server. Remplacez **&lt;integration** **runtime** **name>** par le nom de votre runtime d’intégration.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Pour chiffrer les données sensibles de la charge utile JSON sur le runtime d’intégration auto-hébergé local, nous pouvons exécuter **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** et passer la charge utile JSON ci-dessus. Les informations d’identification sont alors chiffrées à l’aide de l’API de protection des données (DPAPI) avant d’être stockées localement sur le nœud du runtime d’intégration auto-hébergé. La charge utile de sortie peut être redirigée vers un autre fichier JSON (dans ce cas, « encryptedLinkedService.json ») qui contient les informations d’identification chiffrées.

    Remplacez **&lt;integration runtime name&gt;** par le nom de votre runtime d’intégration avant d’exécuter la commande.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Exécutez la commande suivante en utilisant le fichier JSON de l’étape précédente pour créer le service **SqlServerLinkedService** :

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Créez les jeux de données

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Préparer le serveur SQL Server local pour le didacticiel

Dans cette étape, vous allez créer des jeux de données d’entrée et de sortie qui représentent les données d’entrée et de sortie pour l’opération de copie (base de données SQL Server locale = > stockage d’objets blob Azure). Avant de créer des jeux de données, procédez comme suit (des étapes détaillées suivent la liste) :

- Créez une table nommée **emp** dans la base de données SQL Server que vous avez ajoutée en tant que service lié à la fabrique de données, puis insérez quelques exemples d’entrées dans la table.
- Créez un conteneur d’objets blobs nommé **adftutorial** dans le compte de stockage d’objets blobs Azure que vous avez ajouté en tant que service associé à la fabrique de données.


1. Dans la base de données que vous avez spécifiée pour le service lié SQL Server local (**SqlServerLinkedService**), utilisez le script SQL suivant pour créer la table **emp** dans la base de données.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Insérer des exemples dans la table :

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Créer un jeu de données pour la base de données SQL source

1. Créez un fichier JSON sous le nom **SqlServerDataset.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant :  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Pour créer le jeu de données **SqlServerDataset**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Voici l'exemple de sortie :

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Créer un jeu de données pour le stockage Blob Azure récepteur

1. Créez un fichier JSON sous le nom **AzureBlobDataset.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant :

    > [!IMPORTANT]
    > Cet exemple de code suppose que vous disposez d’un conteneur nommé **adftutorial** dans le stockage Blob Azure.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Pour créer le jeu de données **AzureBlobDataset**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Voici l'exemple de sortie :

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Créer des pipelines

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Créer le pipeline « SqlServerToBlobPipeline »

1. Créez un fichier JSON sous le nom **SqlServerToBlobPipeline.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant :

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Pour créer le pipeline **SQLServerToBlobPipeline**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Voici l'exemple de sortie :

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Démarrer et surveiller l’exécution d’un pipeline

1. Démarrez l’exécution du pipeline « SQLServerToBlobPipeline » et capturez l’ID d’exécution du pipeline pour une surveillance ultérieure.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Exécutez le script suivant pour vérifier en permanence l’état d’exécution du pipeline « **SQLServerToBlobPipeline** » et imprimer le résultat final.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Voici la sortie de l’exemple d’exécution :

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Vous pouvez obtenir l’ID d’exécution du pipeline « **SQLServerToBlobPipeline** », puis vérifiez le résultat détaillé de l’exécution d’activité comme suit.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Voici la sortie de l’exemple d’exécution :

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Connectez-vous à votre stockage Blob Azure récepteur et vérifiez que les données y ont bien été copiées à partir d’Azure SQL Database.

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un runtime d’intégration auto-hébergé.
> * Créer et chiffrer un service lié SQL Server local sur le runtime d’intégration auto-hébergé
> * Créer un service lié Stockage Azure.
> * Créer des jeux de données SQL Server et Stockage Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller le pipeline et l’exécution d’activité.

Pour obtenir la liste des magasins de données pris en charge par Azure Data Factory en tant que sources et récepteurs, consultez l’article [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Passez au didacticiel suivant pour découvrir comment copier des données en bloc d’une source vers une destination :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy.md)
