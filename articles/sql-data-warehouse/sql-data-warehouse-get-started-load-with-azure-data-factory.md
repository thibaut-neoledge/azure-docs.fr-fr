<properties
   pageTitle="Chargement de données Azure Data Factory | Microsoft Azure"
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
   ms.date="03/23/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Téléchargement de données avec Azure Data Factory

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 Ce didacticiel vous montre comment créer un pipeline dans Azure Data Factory pour déplacer des données des objets blobs Microsoft Azure Storage vers un entrepôt SQL Data Warehouse. Lors des opérations qui suivent, vous allez :

+ Données d’exemple de configuration dans un objet blob Azure Storage.
+ Connectez des ressources à Azure Data Factory.
+ Créer un pipeline pour déplacer des objets blobs de stockage vers SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## Avant de commencer

Pour vous familiariser avec Azure Data Factory, consultez l’article [Présentation d’Azure Data Factory](../data-factory/data-factory-introduction.md).

### Créer ou identifier des ressources

Avant de commencer ce didacticiel, vous devez disposer des ressources suivantes.

   + **Objet blob Azure Storage** : ce didacticiel utilise l’objet blob Azure Storage comme source de données pour le pipeline Azure Data Factory. Vous devez donc disposer d’un objet blob pour stocker les exemples de données. Si vous n’en possédez pas, découvrez comment [créer un compte de stockage](../storage/storage-create-storage-account/#create-a-storage-accoun/).

   + **SQL Data Warehouse** : ce didacticiel déplace les données entre l’objet blob Azure Storage et l’entrepôt SQL Data Warehouse ; vous avez donc besoin de disposer d’un entrepôt de données en ligne contenant les exemples de données AdventureWorksDW. Si vous n’en possédez pas encore, découvrez comment [approvisionner un entrepôt de données](sql-data-warehouse-get-started-provision.md). Si vous disposez bien d’un entrepôt de données mais que vous ne l’avez pas configuré avec les exemples de données, vous pouvez les [charger manuellement](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Azure Data Factory** : Azure Data Factory exécutera la charge de travail et, par conséquent, vous devez disposer d’une instance que vous pourrez utiliser pour créer le pipeline de déplacement des données. Si vous n’en disposez pas, apprenez à en créer un à l’étape 1 de [Prise en main d’Azure Data Factory (Data Factory Editor)](../data-factory/data-factory-build-your-first-pipeline-using-editor.md).

   + **AZCopy** : vous avez besoin d’AZCopy pour copier les exemples de données de votre client local sur votre objet blob Azure Storage. Pour obtenir des instructions d’installation, consultez la [documentation d’AZCopy](../storage/storage-use-azcopy.md).

## Étape 1 : copier des exemples de données sur l’objet blob Azure Storage

Une fois que tous les éléments sont prêts, vous pouvez copier les exemples de données sur votre objet blob Azure Storage.

1. [Téléchargez les exemples de données](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Ces données ajouteront trois années de données de ventes à vos exemples de données AdventureWorksDW.

2. Utilisez cette commande AZCopy pour copier les trois années de données dans l’objet blob Azure Storage.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Étape 2 : connecter des ressources à Azure Data Factory

Maintenant que les données sont en place, vous pouvez créer le pipeline Azure Data Factory pour déplacer les données entre le stockage d’objets blob Azure et l’entrepôt SQL Data Warehouse.

Pour commencer, ouvrez le [portail Azure](https://portal.azure.com/), puis sélectionnez votre fabrique de données dans le menu de gauche.

### Étape 2.1 : créer un service lié

Liez votre compte de stockage Azure et SQL Data Warehouse à votre fabrique de données.

1. Tout d’abord, commencez le processus d’inscription en cliquant sur la section « Services liés » de votre fabrique de données, puis sur « Nouveau magasin de données. » Choisissez un nom sous lequel inscrire votre compte de stockage Azure, sélectionnez le stockage Azure en tant que type, puis saisissez votre nom de compte ainsi que votre clé de compte.

2. Pour enregistrer SQL Data Warehouse, accédez à la section « Créer et déployer », sélectionnez « Nouveau magasin de données », puis « Azure SQL Data Warehouse ». Copiez et collez ce modèle, puis renseignez vos informations.

```JSON
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
```

### Étape 2.2 : définir le jeu de données

Après avoir créé les services liés, nous devrons définir les jeux de données. Ici, cela signifie que nous devons définir la structure des données déplacées de votre espace de stockage vers votre entrepôt de données. Vous pouvez en savoir plus sur la création

1. Lancez ce processus en accédant à la section « Créer et déployer » de votre fabrique de données.

2. Cliquez sur « Nouveau dataset », puis sur « Stockage des objets blobs Azure » pour lier votre stockage à votre fabrique de données. Vous pouvez utiliser le script ci-dessous pour définir vos données dans le stockage des objets blobs Azure :

```JSON
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
```


3. Maintenant, nous allons définir notre jeu de données pour SQL Data Warehouse. Commencer de la même manière, en cliquant sur « Nouveau dataset », puis sur « Azure SQL Data Warehouse ».

```JSON
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
```

## Étape 3 : créer et exécuter le pipeline

Enfin, nous allons configurer et exécuter le pipeline dans Azure Data Factory. Il s’agit de l’opération qui achève le déplacement effectif des données. Vous trouverez une présentation complète des opérations que vous pouvez réaliser avec SQL Data Warehouse et Azure Data Factory [ici](../data-factory/data-factory-azure-sql-data-warehouse-connector.md).

Dans la section « Créer et déployer », cliquez sur « Autres commandes », puis sur « Nouveau Pipeline ». Après avoir créé le pipeline, vous pouvez utiliser le code ci-dessous pour transférer les données vers votre entrepôt de données :

```JSON
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
```

## Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Parcours d’apprentissage Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).
- [Azure SQL Data Warehouse Connector](../data-factory/data-factory-azure-sql-data-warehouse-connector.md). Il s’agit de la rubrique de référence pour l’utilisation d’Azure Data Factory avec Azure SQL Data Warehouse.


Ces rubriques fournissent des informations détaillées sur Azure Data Factory. Elles décrivent la base de données SQL Azure et HDinsight, mais s’appliquent également à Azure SQL Data Warehouse.

- [Didacticiel : prise en main d’Azure Data Factory](../data-factory/data-factory-build-your-first-pipeline.md). Ce didacticiel est consacré au traitement des données avec Azure Data Factory. Dans ce didacticiel, vous allez apprendre à créer votre premier pipeline qui fait appel à HDInsight pour transformer et analyser des journaux web tous les mois. Notez que ce didacticiel ne couvre aucune activité de copie.
- [Didacticiel : copie de données d’un objet blob Azure Storage vers une base de données SQL Azure](../data-factory/data-factory-get-started.md). Ce didacticiel crée un pipeline dans Azure Data Factory pour copier des données d’un objet blob Azure Storage dans une base de données SQL Azure.
- [Didacticiel présentant un scénario réel](../data-factory/data-factory-tutorial.md). Ce didacticiel décrit de manière approfondie l’utilisation d’Azure Data Factory.

<!---HONumber=AcomDC_0330_2016-->