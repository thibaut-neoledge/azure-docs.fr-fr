---
title: "Copier des données depuis SQL Server vers le stockage Blob à l’aide d’Azure Data Factory | Microsoft Docs"
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: afd7735712d03110a67509a7e94d336219a65b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Didacticiel : Copier des données d’une base de données SQL Server locale vers le stockage Blob Azure
Dans ce didacticiel, vous allez utiliser Azure PowerShell pour créer un pipeline Data Factory qui copie les données d’une base de données SQL Server locale dans un stockage Blob Azure. Vous créerez et utiliserez un runtime d’intégration (IR) auto-hébergé d’Azure Data Factory, qui permet l’intégration de magasins de données locaux et de magasins de données cloud.  Pour en savoir plus sur l’utilisation d’autres outils/SDK pour créer une fabrique de données, consultez [Guides de démarrage rapide](quickstart-create-data-factory-dot-net.md).

Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour une présentation du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md). 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 ou 2016. 
Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données **source**. Créer une table nommée **emp** dans votre base de données SQL Server, puis insérez quelques exemples d’entrées dans la table.

1. Lancez **SQL Server Management Studio**. Si vous utilisez SQL Server 2016, vous devrez peut-être installer SQL Server Management Studio séparément depuis le [centre de téléchargement](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Connectez-vous à votre serveur SQL à l’aide de vos informations d’identification. 
3. Créez un exemple de base de données. Dans l’arborescence, cliquez avec le bouton droit sur **Bases de données**, puis sur **Nouvelle base de données**. Dans la boîte de dialogue **Nouvelle base de données**, entrez un **nom** pour la base de données, puis cliquez sur **OK**. 
4. Exécutez le script de requête suivant sur la base de données, ce qui crée la table **emp**. Dans l’arborescence, cliquez avec le bouton droit sur la **base de données** créée, puis sur **Nouvelle requête**. 

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
2. Sur la base de données qui insère des exemples de données dans la table, exécutez les commandes suivantes :

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Compte de Stockage Azure
Dans ce didacticiel, vous utilisez un compte Stockage Azure à usage général (stockage d’objets Blob spécifiquement) comme banque de données **réceptrice/de destination**. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour en créer un.

#### <a name="get-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce guide de démarrage rapide, vous utilisez le nom et la clé de votre compte Stockage Azure. La procédure suivante détaille les étapes à suivre pour obtenir le nom et la clé de votre compte de stockage. 

1. Lancez un navigateur web et accédez au [portail Azure](https://portal.azure.com). Connectez-vous en utilisant un nom d’utilisateur et un mot de passe Azure. 
2. Cliquez sur **Plus de services >** dans le menu de gauche, filtrez en utilisant le mot clé **Stockage**, puis sélectionnez **Comptes de stockage**.

    ![Rechercher le compte de stockage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire), puis sélectionnez **votre compte de stockage**. 
4. Dans la page **Compte de stockage**, sélectionnez **Clés d’accès** dans le menu.

    ![Obtenir le nom et la clé du compte de stockage](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copiez les valeurs des champs **Nom du compte de stockage** et **key1** dans le presse-papiers. Collez-les dans un bloc-notes ou tout autre éditeur et enregistrez-le. Vous utilisez le nom et la clé du compte de stockage dans le didacticiel. 

#### <a name="create-the-adftutorial-container"></a>Créer le conteneur adftutorial 
Dans cette section, vous allez créer un conteneur d’objets blob nommé adftutorial dans le stockage Blob Azure. 

1. Installez l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) si vous ne l’avez pas sur votre ordinateur. 
2. Lancez l’**Explorateur Stockage Microsoft Azure** sur votre ordinateur.   
3. Dans la fenêtre **Se connecter au Stockage Azure**, sélectionnez **Utiliser un nom et une clé de compte de stockage**, puis cliquez sur **Suivant**. Si vous ne voyez pas la fenêtre **Se connecter au stockage Azure**, cliquez avec le bouton droit sur **Comptes de stockage** dans l’arborescence, puis cliquez sur **Se connecter au stockage Azure**. 

    ![Se connecter au Stockage Azure](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. Dans la fenêtre **Attacher à l’aide du nom et de la clé**, collez le **nom de compte** et la **clé de compte** que vous avez enregistrés à l’étape précédente. Cliquez ensuite sur **Suivant**. 
5. Dans la fenêtre **Résumé de la connexion**, cliquez sur **Se connecter**.
6. Vérifiez que votre compte de stockage s’affiche dans l’arborescence sous **(local et attaché)** -> **Comptes de stockage**. 
7. Développez **Conteneurs d’objets blob** et vérifiez que le conteneur d’objets blob **adftutorial** n’existe pas. S’il existe déjà, ignorez les étapes suivantes pour créer le conteneur. 
8. Cliquez avec le bouton droit sur **Conteneurs d’objets blob** puis, sélectionnez **Créer un conteneur d’objets blob**.

    ![Création du conteneur d’objets blob](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Saisissez **adftutorial** pour le nom et appuyez sur **ENTRÉE**. 
10. Vérifiez que le conteneur **adftutorial** est sélectionné dans l’arborescence. Data Factory crée automatiquement le dossier de sortie de ce conteneur, de sorte que vous n’avez pas besoin d’en créer. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Installation d'Azure PowerShell
Installez la dernière version d’Azure PowerShell, si elle n’est pas installée sur votre ordinateur. 

1. Dans votre navigateur web, accédez à la page [Téléchargez les Kits de développement logiciel et les outils Azure](https://azure.microsoft.com/downloads/). 
2. Cliquez sur **Installation Windows** dans la section **Outils de ligne de commande** -> **PowerShell**. 
3. Pour installer Azure PowerShell, exécutez le fichier **MSI**. 

Pour des instructions détaillées, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Connexion à Azure PowerShell
Lancez **PowerShell** sur votre ordinateur. Gardez Azure PowerShell ouvert jusqu’à la fin de ce guide de démarrage rapide. Si vous fermez puis rouvrez Azure PowerShell, vous devez réexécuter ces commandes.

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

1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. 
   
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
4. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$resourceGroupName` et essayez de nouveau. Si vous souhaitez partager le groupe de ressources avec d’autres, passez à l’étape suivante.  
5. Pour créer la fabrique de données, exécutez l’applet de commande **Set-AzureRmDataFactoryV2** suivante : 
    
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

## <a name="create-a-self-hosted-ir"></a>Créer un runtime d’intégration auto-hébergé

Dans cette section, vous pouvez créer un runtime d’intégration auto-hébergé et l’associer à un nœud local (ordinateur).

1. Créez une variable pour le nom du runtime d’intégration. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Créez un runtime d’intégration auto-hébergé. Utilisez un nom unique au cas où il existe déjà un autre runtime d’intégration de même nom.

   ```powershell
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
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Installer le runtime d’intégration
1. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39717) le runtime d’intégration auto-hébergé sur un ordinateur windows local, puis exécutez l’installation. 
2. Sur la page **Bienvenue dans l’assistant Installation de Microsoft Integration Runtime**, cliquez sur **Suivant**.  
3. Sur la page **Contrat de licence utilisateur final**, acceptez les conditions et le contrat de licence, puis cliquez sur **Suivant**. 
4. Sur la page **Dossier de destination**, cliquez sur **Suivant**. 
5. Sur la page **Prêt à installer Microsoft Integration Runtime**, cliquez sur **Installer**. 
6. Si un message d’avertissement indiquant que l’ordinateur en cours de configuration passera en mode veille ou veille prolongée en cas de non utilisation, s’affiche, cliquez sur **OK**. 
7. Sur la page **Assistant Installation de Microsoft Integration Runtime terminé**, cliquez sur **Terminer**.
8. Sur la page **Inscrire Microsoft Integration Runtime (auto-hébergé)**, collez la clé que vous avez enregistrée dans la section précédente, puis cliquez sur **Inscrire**. 

   ![Inscrire le runtime d’intégration](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Le message suivant s’affiche une fois que le runtime d’intégration auto-hébergé est bien inscrit :

   ![Inscription réussie](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. Sur la page **Nouveau Integration Runtime (auto-hébergé)**, cliquez sur **suivant**. 

    ![Page Nouveau nœud Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. Sur le **Canal de communication Intranet**, cliquez sur **Ignorer**. Vous pouvez sélectionner une certification TLS/SSL pour sécuriser les communications intra-nœud dans un environnement de runtime d’intégration à plusieurs nœuds. 

    ![Page du canal de communication Intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. Sur la page **Inscrire Microsoft Integration Runtime (auto-hébergé)**, cliquez sur **Lancer Configuration Manager**. 
6. La page suivante apparaît une fois que le nœud est connecté au service cloud :

   ![Le nœud est connecté](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Créez des services liés

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Créer un service lié Stockage Azure (destination/réception)

1. Créez un fichier JSON sous le nom **AzureStorageLinkedService.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant : créez le dossier ADFv2Tutorial s’il n’existe pas déjà.  

    > [!IMPORTANT]
    > Remplacez &lt;accountName&gt; et &lt;accountKey&gt; par le nom et la clé de votre compte de stockage Azure avant d’enregistrer le fichier.

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

1. Créez un fichier JSON sous le nom **SqlServerLinkedService.json** dans le dossier **C:\ADFv2Tutorial** avec le contenu suivant : avant d’enregistrer le fichier, remplacez **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** et **&lt;password>** par les valeurs de votre serveur SQL Server. 

    > [!IMPORTANT]
    > Remplacez **&lt;integration** **runtime** **name>** par le nom de votre runtime d’intégration.

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
2. Pour chiffrer les données sensibles de la charge utile JSON sur le runtime d’intégration auto-hébergé local, exécutez **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** et passez la charge utile JSON ci-dessus. Les informations d’identification sont alors chiffrées à l’aide de l’API de protection des données (DPAPI). Les informations d’identification chiffrées sont stockées sur le nœud de runtime d’intégration local auto-hébergé (machine locale). La charge utile de sortie peut être redirigée vers un autre fichier JSON (dans ce cas, « encryptedLinkedService.json ») qui contient les informations d’identification chiffrées.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Exécutez la commande suivante en utilisant le fichier JSON de l’étape précédente pour créer le service **SqlServerLinkedService** :

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous allez créer des jeux de données d’entrée et de sortie qui représentent les données d’entrée et de sortie pour l’opération de copie (base de données SQL Server locale = > stockage d’objets blob Azure).

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
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Vérifier la sortie
Le pipeline crée automatiquement le dossier de sortie nommé `fromonprem` dans le conteneur d’objets blob `adftutorial`. Vérifiez que le fichier **dbo.emp.txt** se trouve dans le dossier de sortie. Utilisez l’[Explorateur de stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que la sortie est créée. 

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

Pour obtenir la liste des magasins de données pris en charge par Azure Data Factory, consultez l’article [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Passez au didacticiel suivant pour découvrir comment copier des données en bloc d’une source vers une destination :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy.md)
