<properties
	pageTitle="Chargement de données Azure Data Factory | Microsoft Azure"
	description="Apprenez à charger des données avec Azure Data Factory"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="lodipalm"/>

# Téléchargement de données avec Azure Data Factory

 L’exécution de ce didacticiel vous montrera comment créer un pipeline dans Azure Data Factory, qui déplacera les données des objets blobs Microsoft Azure Storage vers un entrepôt SQL Data Warehouse. Lors des opérations qui suivent, vous allez :

+ Données d’exemple de configuration dans un objet blob Azure Storage.
+ Connectez des ressources à Azure Data Factory.
+ Créer un pipeline pour déplacer des objets blobs de stockage vers SQL Data Warehouse.

## Ressources
Pour ce didacticiel, vous avez besoin des éléments suivants :

   + **Objet blob de stockage Azure** : votre objet blob de stockage Azure sera la source de données du pipeline. Vous pouvez utiliser un objet blob existant ou en [configurer un](../storage/storage-create-storage-account/).

   + **SQL Data Warehouse** : dans ce didacticiel vous déplacerez les données vers SQL Data Warehouse. Si aucune instance n’est encore installée, vous pouvez apprendre comment en configurer une [ici](sql-data-warehouse-get-started-provision.md). En outre, votre instance devra être configurée avec votre jeu de données AdventureWorks DW. Si vous n’avez pas configuré votre entrepôt de données avec des données d’exemple, vous pouvez le [charger manuellement](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Azure Data Factory** : Azure Data Factory terminera la charge réelle et si vous avez besoin de plus d’informations sur la définition d’Azure Data Factory ou la création de pipelines, vous pouvez cliquer [ici](../data-factory/data-factory-build-your-first-pipeline-using-editor/).

Une fois que tous les éléments sont prêts, vous pouvez passer à la préparation de vos données et la création de votre pipeline Azure Data Factory.

## Exemple de données
En plus des différents éléments du pipeline, nous aurons besoin d’exemples de données pouvant être utilisé pour expérimenter le chargement des données dans Azure Data Factory.

1. Tout d’abord, [téléchargez l’exemple de données](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Ces données fonctionnent conjointement avec les exemples de données que vous avez déjà et représentent trois ans de données commerciales.

2. Une fois que les données sont téléchargées, vous pouvez les transférer vers votre compte de stockage d’objets blob en exécutant le script dans AZCopy :

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	Consultez la [documentation AZCopy](../storage/storage-use-azcopy/) pour plus d’informations sur la façon d’installer AZCopy et de l’utiliser.

Maintenant que les données sont en place, vous pouvez déplacer votre fabrique de données pour créer le pipeline qui déplacera les données de votre compte de stockage vers SQL Data Warehouse.

## Utilisation d’Azure Data Factory
Maintenant que nous avons configuré tous les éléments, nous pouvons commencer à configurer le pipeline en accédant à votre instance d’Azure Data Factory dans le portail Azure en version préliminaire. C’est possible en accédant au [portail Azure](portal.azure.com) et en sélectionnant votre fabrique de données dans le menu de gauche.

À ce stade, il vous reste trois étapes pour configurer votre pipeline de fabrique de données Azure pour transférer des données vers votre entrepôt de données : la liaison de vos services, la définition de vos jeux de données et la création de votre pipeline.

### Création de services liés
La première étape consiste à lier votre compte de stockage Azure et SQL Data Warehouse à votre fabrique de données.

1. Tout d’abord, commencez le processus d’inscription en cliquant sur la section « Services liés » de votre fabrique de données, puis sur « nouveau magasin de données. » Choisissez ensuite un nom sous lequel inscrire votre compte de stockage Azure, sélectionnez le stockage Azure en tant que type, saisissez votre nom de compte, ainsi que votre clé de compte.

2. Pour enregistrer SQL Data Warehouse, vous devez accéder à la section « Créer et déployer », puis sélectionner « Nouveau magasin de données », puis « Azure SQL Data Warehouse ». Vous devez ensuite renseigner le modèle ci-dessous :

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### Enregistrement de jeux de données
Après avoir créé les services liés, nous devrons définir les jeux de données. Ici, cela signifie que nous devons définir la structure des données déplacées de votre espace de stockage vers votre entrepôt de données. Vous pouvez en savoir plus sur la création

1. Lancez ce processus en accédant à la section « Créer et déployer » de votre fabrique de données.

2. Cliquez sur « Nouveau dataset », puis sur « Stockage des objets blobs Azure » pour lier votre stockage à votre fabrique de données. Vous pouvez utiliser le script ci-dessous pour définir vos données dans le stockage des objets blobs Azure :

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
					"format": {
					"type": "TextFormat",
					"columnDelimiter": ",",
					"rowDelimiter": "\n"
					}
				},
				"external": true,
				"availability": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
					}
				}
			}
		}



3. Maintenant, nous allons définir notre jeu de données pour SQL Data Warehouse. Commencer de la même manière, en cliquant sur « Nouveau dataset », puis sur « Azure SQL Data Warehouse ».

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### Configuration de votre pipeline
Enfin, nous allons configurer et exécuter le pipeline dans Azure Data Factory. Il s’agit de l’opération qui achève le déplacement effectif des données. Vous trouverez une présentation complète des opérations que vous pouvez réaliser avec SQL Data Warehouse et Azure Data Factory [ici](../data-factory/data-factory-azure-sql-data-warehouse-connector/).

Dans la section « Créer et déployer », cliquez sur « Autres commandes », puis sur « Nouveau Pipeline ». Après avoir créé le pipeline, vous pouvez utiliser le code ci-dessous pour transférer les données vers votre entrepôt de données :

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
					}
				],
				"policy": {
					"timeout": "01:00:00",
					"concurrency": 1
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=Oct15_HO4-->