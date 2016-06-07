<properties
	pageTitle="Créer votre première fabrique de données Azure (modèle ARM) | Microsoft Azure"
	description="Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory en utilisant un modèle Azure Resource Manager."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/16/2016"
	ms.author="spelluru"/>

# Didacticiel : concevoir votre première fabrique de données Azure à l’aide du modèle Azure Resource Manager
> [AZURE.SELECTOR]
- [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md)
- [Utilisation de Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Utiliser PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Utilisation de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Utilisation du modèle Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)


Dans cet article, vous allez apprendre à utiliser un modèle Azure Resource Manager pour créer votre première fabrique de données Azure.


## Conditions préalables
Outre les conditions préalables répertoriées dans la rubrique Vue d’ensemble du didacticiel, vous devez installer le composant suivant :

- Vous **devez** lire l’article [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et effectuer les étapes préalables avant de continuer. 
- **Installez Azure PowerShell**. Suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour installer la dernière version d’Azure PowerShell sur votre ordinateur.
- Cet article ne fournit pas de vue d’ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, lisez l’article [Introduction à Azure Data Factory](data-factory-introduction.md). 
- Consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md) pour en savoir plus sur les modèles Azure Resource Manager. 

> [AZURE.IMPORTANT]
Vous devez exécuter les étapes préalables énumérées [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) pour effectuer la procédure pas à pas décrite dans cet article.

## Créer un modèle ARM

Créez un fichier JSON nommé **ADFTutorialARM.json** dans le dossier **C:\\ADFGetStarted** avec le contenu suivant :

Le modèle vous permet de créer les entités suivantes de la fabrique de données.

1. Une **fabrique de données** nommée **TutorialDataFactoryARM**. Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline peut contenir une ou plusieurs activités. Par exemple, une activité de copie pour copier des données d’une source vers un magasin de données de destination, et une activité Hive HDInsight pour exécuter un script Hive pour transformer des données d’entrée et produire des données de sortie. 
2. Deux **services liés** : **StorageLinkedService** et **HDInsightOnDemandLinkedService**. Ces services liés lient votre compte Azure Storage et un cluster Azure HDInsight à la demande à votre fabrique de données. Le compte de stockage Azure contient les données d’entrée et de sortie pour le pipeline de cet exemple. Le service lié HDInsight est utilisé pour exécuter le script Hive spécifié dans l’activité du pipeline de cet exemple. Vous devez identifier les services de magasin de données/de calcul qui sont utilisés dans votre scénario et les lier à la fabrique de données en créant des services liés. 
3. Deux **jeux de données** (entré/sortie) : **AzureBlobInput** et **AzureBlobOutput**. Ces jeux de données représentent les données d’entrée et de sortie pour le traitement Hive. Ces jeux de données font référence au service **StorageLinkedService** que vous avez créé précédemment dans ce didacticiel. Le service lié pointe vers un compte de stockage Azure, et les jeux de données spécifient le conteneur, le dossier et le nom de fichier dans le stockage qui contient les données d’entrée et de sortie.   

Cliquez sur l’onglet **Utilisation de Data Factory Editor** pour passer à l’article contenant des détails sur les propriétés JSON utilisées dans ce modèle.

> [AZURE.IMPORTANT] Changez les valeurs des variables **storageAccountName** et **storageAccountKey**. Changez également **dataFactoryName**, car le nom doit être unique.


	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
	        "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "AzureStorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
        					"typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
            					"timeToLive": "00:05:00",
                                "osType": "windows",
            					"linkedServiceName": "[variables('storageLinkedServiceName')]",
    						}
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
						    }
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        }
						    }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2016-04-01T00:00:00Z",
						        "end": "2016-04-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}

Cliquez sur l’onglet **Utilisation de Data Factory Editor** pour passer à l’article contenant des détails sur les propriétés JSON utilisées dans ce modèle.

Notez les points suivants :

- La fabrique de données crée pour vous un cluster HDInsight **Windows** avec le JSON ci-dessus. Vous pouvez également lui faire créer un cluster HDInsight **Linux**. Consultez [Service lié HDInsight à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) pour plus d’informations. 
- Vous pouvez utiliser **votre propre cluster HDInsight** au lieu d’utiliser un cluster HDInsight à la demande. Consultez [Service lié HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) pour plus d’informations.
- Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. C’est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il existe un cluster activé (**timeToLive**) et est supprimé une fois le traitement activé.

	Comme un nombre croissant de tranches sont traitées, vous verrez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs suit un modèle : « adf**yourdatafactoryname**-**linkedservicename**- datetimestamp ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.

Consultez [Service lié HDInsight à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) pour plus d’informations.

> [AZURE.NOTE] Vous pouvez trouver un autre exemple de modèle ARM pour créer une fabrique de données Azure sur [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Créer une fabrique de données

1. Démarrez **Azure PowerShell** et exécutez la commande suivante. 
	- Exécutez **Login-AzureRmAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.  
	- Exécutez la commande suivante pour sélectionner un abonnement dans lequel vous souhaitez créer la fabrique de données. Get-AzureRmSubscription - SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
1. Exécutez la commande suivante pour déployer des entités Data Factory à l’aide du modèle ARM que vous avez créé à l’étape 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Surveillance d’un pipeline
 
1.	Après la connexion au [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir** et sélectionnez **Fabriques de données**. ![Parcourir tout -> Fabriques de données](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Dans le panneau **Fabriques de données**, cliquez sur la fabrique de données (**TutorialFactoryARM**) que vous avez créée.	
2.	Dans le panneau **Fabrique de données** de votre fabrique de données, cliquez sur **Diagramme**. ![Vignette du diagramme](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	Dans la **Vue de diagramme**, une vue d’ensemble des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche.
	
	![Vue du diagramme](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dans la vue schématique, double-cliquez sur le jeu de données **AzureBlobOutput**. Le segment est en cours de traitement.

	![Jeu de données](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Quand le traitement est terminé, l’état du segment devient **Prêt**. Notez que la création d’un cluster HDInsight à la demande prend généralement un certain temps (environ 20 minutes). 

	![Jeu de données](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Quand l’état du segment est **Prêt**, vérifiez la présence des données de sortie dans le dossier **partitioneddata** du conteneur **adfgetstarted** de votre stockage d’objets blob.  

Consultez [Surveiller les jeux de données et le pipeline](data-factory-monitor-manage-pipelines.md) pour obtenir des instructions sur l’utilisation des panneaux du portail Azure pour surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.

Vous pouvez également utiliser l’application Surveillance et gestion pour surveiller vos pipelines de données. Pour en savoir plus sur l’utilisation de l’application, consultez l’article [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion](data-factory-monitor-manage-app.md).

> [AZURE.IMPORTANT] Le fichier d’entrée sera supprimé lorsque la tranche est traitée avec succès. Par conséquent, si vous souhaitez réexécuter la tranche ou refaire le didacticiel, chargez le fichier d’entrée (input.log) dans le dossier inputdata du conteneur adfgetstarted.

## Modèle ARM pour la création d’une passerelle
Voici un exemple de modèle ARM pour la création d’une passerelle logique à l’arrière. Notez que vous devez installer une passerelle sur votre ordinateur local ou sur une machine virtuelle IaaS Azure et l’enregistrer auprès du service Data Factory à l’aide d’une clé. Pour plus d’informations, consultez [Déplacer des données entre un emplacement local et le cloud](data-factory-move-data-between-onprem-and-cloud.md).

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "GatewayUsingArmDF",
	        "apiVersion": "2015-10-01",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "eastus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "gateways",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "name": "GatewayUsingARM",
	                    "properties": {
	                    	"description": "my gateway"
						}
	                }            
				]
	        }
	    ]
	}

Ce modèle crée une fabrique de données nommée GatewayUsingArmDF avec une passerelle nommée GatewayUsingARM.

## Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Activités de transformation des données](data-factory-data-transformation-activities.md) | Cet article fournit une liste des activités de transformation de données (par exemple, la transformation Hive HDInsight que vous avez utilisée dans ce didacticiel) prises en charge par Azure Data Factory. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory et comment les utiliser pour créer des flux de travail pilotés par les données de bout en bout pour votre scénario ou votre entreprise. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article va vous aider à comprendre les jeux de données dans Azure Data Factory.
| [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) | Cet article décrit comment surveiller, gérer et déboguer les pipelines à l’aide de l’application de surveillance et gestion. 

  

<!---HONumber=AcomDC_0601_2016-->