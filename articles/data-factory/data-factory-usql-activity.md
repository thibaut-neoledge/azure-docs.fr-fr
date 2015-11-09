<properties 
	pageTitle="Exécution d'un script U-SQL sur Azure Data Lake Analytics à partir de la fabrique d'Azure Data Factory" 
	description="Découvrez comment traiter les données en exécutant des scripts U-SQL sur le service de calcul Azure Data Lake Analytics." 
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
	ms.topic="article" 
	ms.date="10/27/2015" 
	ms.author="spelluru"/>

# Exécution d'un script U-SQL sur Azure Data Lake Analytics à partir de la fabrique d'Azure Data Factory 
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l'**activité U-SQL de Data Lake Analytics** qui exécute un script **U-SQL** sur un service lié de calcul **Azure Data Lake Analytics**.

> [AZURE.NOTE]Vous devez créer un compte Azure Data Lake Analytics avant de créer un pipeline avec une activité U-SQL Data Lake Analytics. Pour plus d'informations sur Azure Data Lake Analytics, consultez [Prise en main d'Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Veuillez consulter le [didacticiel Concevez votre premier pipeline](data-factory-build-your-first-pipeline.md) pour connaître les étapes détaillées de création d'une fabrique de données, de services liés, de jeux de données et d'un pipeline. Utilisez les extraits de code JSON avec Data Factory Editor ou Visual Studio ou Azure PowerShell pour créer les entités Data Factory.

## Service lié Azure Data Lake Analytics
Vous créez un service lié **Azure Data Lake Analytics** pour lier un service de calcul Azure Data Lake Analytics à une fabrique de données Azure avant d'utiliser l'activité U-SQL Data Lake Analytics dans un pipeline.

L'exemple suivant présente la définition JSON pour un service lié Azure Data Lake Analytics.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


Le tableau suivant décrit les propriétés utilisées dans la définition JSON.

Propriété | Description | Requis
-------- | ----------- | --------
Type | La propriété de type doit être définie sur : **AzureDataLakeAnalytics**. | Oui
accountName | Nom du compte Azure Data Lake Analytics. | Oui
dataLakeAnalyticsUri | URI Azure Data Lake Analytics. | Non 
autorisation | Le code d'autorisation est automatiquement récupéré après un clic sur le bouton **Autoriser** dans Data Factory Editor et une fois la connexion OAuth effectuée. | Oui 
subscriptionId | ID d'abonnement Azure | Non (si non spécifié, l'abonnement de la fabrique de données est utilisé). 
nom\_groupe\_ressources | Nom du groupe de ressources Azure | Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé).
sessionId | ID de session issu de la session d'autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu'une seule fois. Il est généré automatiquement dans Data Factory Editor. | Oui

   
 
## Activité U-SQL Data Lake Analytics 

L'extrait de code JSON suivant définit un pipeline avec une activité U-SQL Data Lake Analytics. La définition d'activité comporte une référence au service lié Azure Data Lake Analytics créé précédemment.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


Le tableau suivant indique les noms et les descriptions des propriétés qui sont spécifiques à cette activité.

Propriété | Description | Requis
:-------- | :----------- | :--------
type | La propriété de type doit être définie sur **DataLakeAnalyticsU-SQL**. | Oui
scriptPath | Chemin d'accès au dossier qui contient le script SQL-U. | Non (si vous utilisez le script)
scriptLinkedService | Service lié qui lie le stockage qui contient le script à la fabrique de données | Non (si vous utilisez le script)
script | Spécifiez un script en ligne au lieu de spécifier scriptPath et scriptLinkedService. Par exemple : « script » : « Test CRÉER BASE DE DONNÉES ». | Non (si vous utilisez scriptPath et scriptLinkedService)
degreeOfParallelism | Le nombre maximal de nœuds qui seront utilisés simultanément pour exécuter le travail. | Non
priority | Détermine les travaux parmi tous ceux qui sont en file d'attente qui doivent être sélectionnés pour s'exécuter en premier. Plus le numéro est faible, plus la priorité est élevée. | Non 
parameters | Paramètres du script U-SQL | Non 


### Exemples de jeux de données d'entrée et de sortie

#### Jeu de données d'entrée
Dans cet exemple, les données d'entrée se trouvent dans un magasin Azure Data Lake (fichier SearchLog.tsv dans le dossier datalake/input).

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Jeu de données de sortie
Dans cet exemple, les données de sortie générées par le script U-SQL sont stockées dans un magasin Azure Data Lake (dossier datalake/output).

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Exemple de service lié Azure Data Lake Store
Voici la définition de l'exemple de service lié Azure Data Lake Store utilisé par les jeux de données d'entrée/de sortie ci-dessus.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

Consultez [Déplacer des données vers et depuis Azure Data Lake Store](data-factory-azure-datalake-connector.md) pour obtenir une description des propriétés JSON dans le service lié Azure Data Lake Store et les extraits de code JSON du jeu de données ci-dessus.

<!---HONumber=Nov15_HO1-->