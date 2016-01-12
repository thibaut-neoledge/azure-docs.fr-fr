<properties
	pageTitle="Prise en main d’Azure Data Factory (modèle Azure Resource Manager)"
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
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Concevez votre premier pipeline Azure Data Factory en utilisant le modèle Gestionnaire de ressources Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Dans cet article, vous allez apprendre à utiliser un modèle Azure Resource Manager pour créer votre première fabrique de données Azure.


## Conditions préalables
Outre les conditions préalables répertoriées dans la rubrique Vue d’ensemble du didacticiel, vous devez installer le composant suivant :

- Vous **devez** lire l’article [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et effectuer les étapes préalables avant de continuer. 
- **Installez Azure PowerShell**. Suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour installer la dernière version d’Azure PowerShell sur votre ordinateur.
- Cet article ne fournit pas de vue d’ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, lisez l’article [Introduction à Azure Data Factory](data-factory-introduction.md). 
- Consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md) pour en savoir plus sur les modèles Azure Resource Manager. 
 

## Étape 1 : Création du modèle ARM

Créez un fichier JSON nommé **ADFTutorialARM.json** dans le dossier **C:\\ADFGetStarted** avec le contenu suivant :

> [AZURE.IMPORTANT]Changez les valeurs des variables **storageAccountName** et **storageAccountKey**. Changez également **dataFactoryName**, car le nom doit être unique.

Le modèle vous permet de créer les entités suivantes de la fabrique de données.

1. Une **fabrique de données** nommée **TutorialDataFactoryARM**. Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline peut contenir une ou plusieurs activités. Par exemple, une activité de copie pour copier des données d’une source vers un magasin de données de destination, et une activité Hive HDInsight pour exécuter un script Hive pour transformer des données d’entrée et produire des données de sortie. 
2. Deux **services liés** : **StorageLinkedService** et **HDInsightOnDemandLinkedService**. Ces services liés lient votre compte Azure Storage et un cluster Azure HDInsight à la demande à votre fabrique de données. Le compte de stockage Azure contient les données d’entrée et de sortie pour le pipeline de cet exemple. Le service lié HDInsight est utilisé pour exécuter le script Hive spécifié dans l’activité du pipeline de cet exemple. Vous devez identifier les services de magasin de données/de calcul qui sont utilisés dans votre scénario et les lier à la fabrique de données en créant des services liés. 
3. Deux **jeux de données** (entré/sortie) : **AzureBlobInput** et **AzureBlobOutput**. Ces jeux de données représentent les données d’entrée et de sortie pour le traitement Hive. Ces jeux de données font référence au service **StorageLinkedService** que vous avez créé précédemment dans ce didacticiel. Le service lié pointe vers un compte de stockage Azure, et les jeux de données spécifient le conteneur, le dossier et le nom de fichier dans le stockage qui contient les données d’entrée et de sortie.   

Cliquez sur l’onglet **Utilisation de Data Factory Editor** pour passer à l’article contenant des détails sur les propriétés JSON utilisées dans ce modèle.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
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
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
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
						        "linkedServiceName": "StoraegLinkedService",
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
						        "linkedServiceName": "StorageLinkedService",
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
						                    "scriptLinkedService": "StorageLinkedService",
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
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}



## Étape 2 : Déployer des entités Data Factory à l’aide du modèle ARM

1. Démarrez Azure PowerShell et exécutez la commande suivante. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous la fermez, puis la rouvrez, vous devez réexécuter ces commandes.
	- Exécutez **Login-AzureRmAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription Nom\_abonnement** pour sélectionner l’abonnement que vous voulez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure.
1. Exécutez la commande suivante pour déployer des entités Data Factory à l’aide du modèle ARM que vous avez créé à l’étape 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Surveiller le pipeline
 
1.	Après la connexion au [portail Azure](http://portal.azure.com/), cliquez sur **Parcourir** et sélectionnez **Fabriques de données**. 
		![Parcourir tout -> Fabriques de données](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Dans le panneau **Fabriques de données**, cliquez sur la fabrique de données (**TutorialFactoryARM**) que vous avez créée.	
2.	Dans le panneau **Fabrique de données** de votre fabrique de données, cliquez sur **Diagramme**. 
		![Vignette du diagramme](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	Dans la **Vue de diagramme**, une vue d’ensemble des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche.
	
	![Vue du diagramme](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dans la vue schématique, double-cliquez sur le jeu de données **AzureBlobOutput**. Le segment est en cours de traitement.

	![Jeu de données](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Quand le traitement est terminé, l’état du segment devient **Prêt**. Notez que la création d’un cluster HDInsight à la demande prend généralement un certain temps (environ 20 minutes). 

	![Jeu de données](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Quand l’état du segment est **Prêt**, vérifiez la présence des données de sortie dans le dossier **partitioneddata** du conteneur **adfgetstarted** de votre stockage d’objets blob.  
 

<!---HONumber=AcomDC_0107_2016-->