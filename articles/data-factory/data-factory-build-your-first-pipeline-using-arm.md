<properties
	pageTitle="Concevez votre premier pipeline Azure Data Factory en utilisant Azure PowerShell"
	description="Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l'aide d'Azure PowerShell."
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
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# Concevez votre premier pipeline Azure Data Factory en utilisant Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Dans cet article, vous apprendrez à vous servir de modèles Azure Resource Manager pour créer votre premier pipeline. Ce didacticiel se déroule comme suit :

1.	Création de la fabrique de données
2.	Création des services liés (magasins de données, calculs) et des jeux de données
3.	Création du pipeline

 

## Composants requis
Outre les conditions préalables répertoriées dans la rubrique Vue d’ensemble du didacticiel, vous devez installer le composant suivant :

- **Installez Azure PowerShell**. Suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour installer la dernière version d’Azure PowerShell sur votre ordinateur.
- Cet article ne fournit pas une vue d'ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, consultez l’article [Présentation d’Azure Data Factory](data-factory-introduction.md). 
- Voir [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md) pour en savoir plus sur les modèles Azure Resource Manager (ARM). 
 

## Étape 1 : Création du modèle ARM

Créez un fichier JSON nommé **ADFTutorialARM.json** dans dossier **C:\\ADFGetStarted** avec le contenu suivant :

> [AZURE.IMPORTANT]Modifiez les valeurs des variables **storageAccountName** et **storageAccountKey**. Modifiez également le nom **dataFactoryName**, car ce dernier doit être unique.

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
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                      "properties": {
	                        "type": "AzureBlob",
	                        "linkedServiceName": "StorageLinkedService",
	                        "typeProperties": {
	                          "folderPath": "data/partitioneddata",
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
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
	                        "properties": {
	                            "description": "My first Azure Data Factory pipeline using ARM",
	                            "activities": [
	                              {
	                                "type": "HDInsightHive",
	                                "typeProperties": {
	                                  "scriptPath": "script/partitionweblogs.hql",
	                                  "scriptLinkedService": "StorageLinkedService",
	                                  "defines": {
                                        "partitionedtable": "[concat('wasb://data@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"	                                  }
	                                },
	                                "outputs": [
	                                  {
	                                    "name": "[variables('blobOutputDataset')]"
	                                  }
	                                ],
	                                "scheduler": {
	                                    "frequency": "Month",
	                                    "interval": 1
	                                },
	                                "policy": {
	                                  "concurrency": 1,
	                                  "retry": 3
	                                },
	                                "name": "RunSampleHiveActivity",
	                                "linkedServiceName": "HDInsightOnDemandLinkedService"
	                              }
	                            ],
	                            "start": "2014-01-01",
	                            "end": "2014-01-02"
	                        }
	                    }
	            ]
	        }
	    ]
	}


## Étape 2 : Déployer des entités Data Factory à l’aide du modèle ARM

1. Démarrez Azure PowerShell et exécutez la commande suivante. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous la fermez, puis la rouvrez, vous devez exécuter ces commandes à nouveau.
	- Exécutez **Login-AzureRmAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription SubscriptionName** pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure.
1. Exécutez la commande suivante pour déployer des entités Data Factory à l’aide du modèle ARM que vous avez créé à l’étape 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Surveiller le pipeline
 
1.	Après la connexion au [portail Azure](http://portal.azure.com/), cliquez sur **Parcourir** et sélectionnez **Fabriques de données**. ![Parcourir tout -> Fabriques de données](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Dans le panneau **Fabriques de données**, cliquez sur la fabrique de données (**TutorialFactoryARM**) que vous avez créée.	
2.	Dans le panneau **Fabrique de données** de votre fabrique de données, cliquez sur **Diagramme**. ![Vignette schématique](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	Dans la **vue schématique**, une vue d’ensemble des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche.
	
	![Vue schématique](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dans la vue schématique, double-cliquez sur le jeu de données **AzureBlobOutput**. Le segment est en cours de traitement.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Lorsque le traitement est terminé, l’état du segment devient **Ready**. Notez que la création d'un cluster HDInsight à la demande prend généralement un certain temps. 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Lorsque la tranche indique l’état **Prêt**, vérifiez le dossier **partitioneddata** du conteneur de données de votre stockage d’objets blob pour les **données** de sortie.  
 

<!---HONumber=AcomDC_1217_2015-->