<properties
   pageTitle="Création de clusters Hadoop à la demande basés sur Linux dans HDInsight avec Azure Data Factory | Microsoft Azure"
   	description="Apprenez à créer des clusters HDInsight à la demande avec Azure Data Factory."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# Création de clusters Hadoop à la demande basés sur Linux dans HDInsight avec Azure Data Factory

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) est un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données. Dans cet article, vous allez apprendre comment utiliser Azure Data Factory pour créer un [service lié Azure HDInsight à la demande](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) et comment utiliser le cluster pour exécuter une tâche Hive. Voici le processus général :

1. Créer un cluster HDInsight à la demande.
2. Exécuter une tâche Hive pour lire les données brutes des journaux web à partir d’un compte de stockage d’objets blob source, transformer les données et écrire le résultat dans un compte de stockage d’objets blob de destination.
3. Supprimer le cluster en fonction du paramètre de durée de vie.

L’activité Hive définie dans le pipeline de la fabrique de données appelle un script HiveQL prédéfini. Le script crée une table externe qui fait référence aux données brutes des journaux web stockées dans le stockage d’objets blob Azure, puis partitionne les données brutes par année et par mois.

Voici les échantillons de lignes pour chaque mois du fichier d’entrée.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Le script crée trois dossiers de résultat en fonction de l’entrée précédente. Chaque dossier contient un fichier avec les entrées correspondant à chaque mois.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Pour obtenir la liste des activités de transformation de données de Data Factory en plus de l’activité Hive, consultez [Transformation et analyse en utilisant Azure Data Factory](../data-factory/data-factory-data-transformation-activities.md).

Il existe de nombreux avantages à l’utilisation de HDInsight avec Data Factory :

- La facturation des clusters HDInsight s’effectue à la minute, que vous les utilisiez ou non. Avec Data Factory, les clusters sont créés à la demande. Et les clusters sont automatiquement supprimés lorsque les tâches sont terminées. Par conséquent, vous ne payez que pour le temps d’exécution de la tâche et la courte durée d’inactivité (time-to-live).
- Vous pouvez créer un flux de travail à l’aide du pipeline Data Factory.
- Vous pouvez planifier des tâches récursives.

##Configuration requise :

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- [Abonnement Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Interface de ligne de commande Azure ou Azure PowerShell.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

##Préparer le compte de stockage

Vous pouvez utiliser jusqu’à trois comptes de stockage dans ce scénario :

- le compte de stockage par défaut du cluster HDInsight
- le compte de stockage des données d’entrée
- le compte de stockage des données de sortie

Pour simplifier ce didacticiel, vous allez utiliser un seul compte de stockage pour ces trois fonctions. L’exemple de script d’interface de ligne de commande Azure et Azure PowerShell de cette section effectue les opérations suivantes :

1. Connexion à Azure.
2. Création d’un groupe de ressources Azure.
3. Création d’un compte Azure Storage.
4. Création d’un conteneur d’objets blob dans le compte de stockage.
5. Copie des deux fichiers suivants dans le conteneur d’objets blob :

    - Fichier d’entrée : [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - Script HiveQL : [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    Les deux fichiers sont stockés dans un conteneur d’objets blob public.

>[AZURE.IMPORTANT] Notez le nom du groupe de ressources, le nom du compte de stockage et la clé du compte de stockage utilisés dans votre script. Vous en aurez besoin dans la prochaine section.

**Pour préparer le stockage et copier les fichiers à l’aide de l’interface de ligne de commande Azure**

    azure login
    
    azure config mode arm

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

Le nom du conteneur est *adfgetstarted*. Gardez-le tel quel. Dans le cas contraire, vous devez mettre à jour le modèle Resource Manager.

Si vous avez besoin d’aide avec ce script d’interface de ligne de commande, consultez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../storage/storage-azure-cli.md).

**Pour préparer le stockage et copier les fichiers à l’aide d’Azure PowerShell**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = (Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName)[0].Value

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

Si vous avez besoin d’aide avec ce script PowerShell, consultez [Utilisation d’Azure PowerShell avec Azure Storage](../storage/storage-powershell-guide-full.md).

**Pour examiner le compte de stockage et son contenu**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Groupes de ressources** dans le volet de gauche.
3. Double-cliquez sur le nom du groupe de ressources que vous avez créé dans votre interface de ligne de commande ou votre script PowerShell. Utilisez le filtre si la liste des groupes de ressources est trop longue.
4. Dans la mosaïque **Ressources**, vous devez voir une ressource, sauf si vous partagez le groupe de ressources avec d’autres projets. Il s’agit du compte de stockage avec le nom que vous avez spécifié précédemment. Cliquez sur le nom du compte de stockage.
5. Cliquez sur la mosaïque **Objets Blob**.
6. Cliquez sur le conteneur **adfgetstarted**. Vous voyez deux dossiers : **input data** et **script**.
7. Ouvrez le dossier et vérifiez les fichiers des deux dossiers.
 
## Créer une fabrique de données

Avec le compte de stockage, les données d’entrée et le script HiveQL préparé, vous êtes prêt à créer une fabrique de données Azure. Il existe plusieurs méthodes pour créer la fabrique de données. Vous allez utiliser le portail Azure pour appeler un modèle Resource Manager personnalisé dans ce didacticiel. Vous pouvez également appeler le modèle Resource Manager depuis l’[interface de ligne de commande Azure](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) et [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell). Pour les autres méthodes de création de fabriques de données, consultez la page [Didacticiel : créer votre première fabrique de données](../data-factory/data-factory-build-your-first-pipeline.md).

Le modèle Resource Manager de niveau supérieur contient :

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Une ressource de fabrique de données appelée *hdinsight-hive-on-demand* (le nom n’est pas affiché sur la capture d’écran). Data Factory est actuellement uniquement pris en charge dans les régions États-Unis de l’Ouest et Europe du Nord.

La ressource *hdinsight-hive-on-demand* contient 4 ressources :

- Un LinkedService au compte de stockage qui sera utilisé comme compte de stockage HDInsight par défaut, le stockage des données d’entrée et le stockage des données de résultat.
- Un LinkedService à la création du cluster HDInsight :

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    Même si ce n’est pas spécifié, le cluster est créé dans la même région que le compte de stockage.
    
    Notez le paramètre *timeToLive*. La fabrique de données supprime automatiquement le cluster quand celui-ci est inactif pendant 30 minutes.
- Un jeu de données pour les données d’entrée. Le nom du fichier et le nom du dossier sont définis ici :

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- Un jeu de données pour les données de résultat, puis un pipeline pour le traitement des données. Le chemin du résultat est défini ici :
        
        "folderPath": "adfgetstarted/partitioneddata",

    Le paramètre [dataset availability](../data-factory/data-factory-create-datasets.md#Availability) se présente comme suit :
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    Dans Azure Data Factory, la disponibilité du jeu de données de résultats conditionne le pipeline. Cela signifie que la tranche est exécutée tous les mois, le dernier jour du mois. Pour plus d’informations, consultez [Planification et exécution avec Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    La définition du pipeline est la suivante :
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    Il contient une activité. Le *début* et la *fin* de l’activité ont une date passée, ce qui signifie qu’il n’y aura qu’une seule tranche. Si la fin est une date à venir, la fabrique de données crée une autre tranche en temps voulu. Pour plus d’informations, consultez [Planification et exécution avec Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    Voici la définition de l’activité :
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    Les entrées, les résultats et le chemin d’accès du script sont définis.
    
**Pour créer une fabrique de données**

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure. Le modèle se trouve dans https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/fr-FR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Entrez **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** et **STORAGEACCOUNTKEY** pour le compte créé dans la dernière section, puis cliquez sur **OK**. Le nom Data Factory doit être globalement unique.
3. Dans **Groupe de ressources**, sélectionnez le même groupe de ressources que celui que vous avez utilisé dans la dernière section.
4. Cliquez sur **Conditions juridiques**, puis sur **Créer**.
5. Cliquez sur **Create**. La mosaïque **Déploiement du modèle de déploiement** apparaît sur le tableau de bord. Attendez que le texte de la mosaïque prenne le nom du groupe de ressources. La création d’un cluster HDInsight peut prendre environ 20 minutes.
6. Cliquez sur la mosaïque pour ouvrir le groupe de ressources. Vous devez maintenant voir une autre ressource de fabrique de données en plus de la ressource du compte de stockage.
7. Cliquez sur **hdinsight-hive-on-demand**.
8. Cliquez sur la mosaïque **Diagramme**. Le diagramme montre une activité avec un jeu de données d’entrée et un jeu de données de sortie :

    ![Diagramme du pipeline d’activité HDInsight à la demande avec Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    Les noms sont définis dans le modèle Resource Manager.
9. Double-cliquez sur **AzureBlobOutput**.
10. Dans **Tranches récemment mises à jour**, une tranche doit s’afficher. Si l’état est **En cours**, attendez jusqu’à ce qu’il passe à **Prêt**.

**Pour vérifier le résultat de la fabrique de données**

1. Utilisez la même procédure dans la dernière session pour vérifier le contenu du conteneur adfgetstarted. Il existe deux nouveaux conteneurs en plus de **adfgetsarted** :

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx : il s’agit du conteneur par défaut pour le cluster HDInsight. Le nom du conteneur par défaut suit le modèle : « adf>yourdatafactoryname>-linkedservicename-datetimestamp ».
    - adfjobs : il s’agit du conteneur des journaux de tâche ADF.
    
    Le résultat de la fabrique de données est stocké dans afgetstarted, que vous avez configuré dans le modèle Resource Manager.
2. Cliquez sur **adfgetstarted**.
3. Double-cliquez sur **partitioneddata**. Un dossier **year = 2014** s’affiche, car tous les journaux web datent de l’année 2014.

    ![Sortie du pipeline d’activité HDInsight à la demande avec Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Si vous ouvrez la liste, vous devez voir 3 dossiers pour janvier, février et mars. Il y a un journal pour chaque mois.

    ![Sortie du pipeline d’activité HDInsight à la demande avec Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##Nettoyage du didacticiel

Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il existe un cluster activé (timeToLive). Le cluster est supprimé une fois le traitement terminé. Pour chaque cluster, Azure Data Factory crée un stockage d’objets blob Azure, utilisé comme système de fichiers par défaut pour le cluster. Bien que le cluster HDInsight soit supprimé, le conteneur de stockage d’objets blob par défaut et le compte de stockage associé ne sont pas supprimés. C’est normal. Comme un nombre croissant de tranches sont traitées, vous verrez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs suit un modèle : « adfyourdatafactoryname-linkedservicename-datetimestamp ».

[Azure Resource Manager](../resource-group-overview.md) est utilisé pour déployer, gérer et surveiller votre solution en tant que groupe. La suppression du groupe de ressources supprime tous les composants à l’intérieur du groupe.

**Pour supprimer le groupe de ressources**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Groupes de ressources** dans le volet de gauche.
3. Double-cliquez sur le nom du groupe de ressources que vous avez créé dans votre interface de ligne de commande ou votre script PowerShell. Utilisez le filtre si la liste des groupes de ressources est trop longue. Il ouvre le groupe de ressources dans un nouveau panneau.
4. Dans la mosaïque **Ressources**, vous devez voir le compte de stockage par défaut et la fabrique de données, sauf si vous partagez le groupe de ressources avec d’autres projets.
5. Cliquez sur **Supprimer** en haut du panneau. Ce faisant, vous supprimez également le compte de stockage et les données stockées dans celui-ci.
6. Entrez le nom du groupe de ressources, puis cliquez sur **Supprimer**.

Au cas où vous ne souhaitez pas supprimer le compte de stockage en même temps que le groupe de ressources, vous pouvez envisager l’architecture suivante en séparant les données d’entreprise du compte de stockage par défaut. Dans ce cas, vous avez un groupe de ressources pour le compte de stockage avec les données d’entreprise et un autre groupe de ressources pour le compte de stockage par défaut et la fabrique de données. La suppression du deuxième groupe de ressources n’a aucune incidence sur le compte de stockage de données d’entreprise. Pour ce faire :

- Ajoutez le code suivant au groupe de ressources de niveau supérieur avec la ressource Microsoft.DataFactory/datafactories dans votre modèle Resource Manager. Cette opération crée un compte de stockage :

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- Ajoutez un nouveau point de service lié au nouveau compte de stockage :

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- Configurez le LinkedService HDInsight à la demande avec un dependsOn supplémentaire et un additionalLinkedServiceNames :

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##Étapes suivantes
Dans cet article, vous avez appris comment utiliser Azure Data Factory pour créer un cluster HDInsight à la demande pour traiter des tâches Hive. En savoir plus :

- [Didacticiel Hadoop : prise en main de Hadoop sous Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Documentation HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Documentation Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0914_2016-->