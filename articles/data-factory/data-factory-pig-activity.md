<properties 
	pageTitle="Activité pig" 
	description="Découvrez comment utiliser l'activité pig d’une fabrique de données Azure pour exécuter des requêtes pig sur un cluster HDInsight à la demande/ou votre propre cluster." 
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
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Activité pig

L'activité HDInsight pig d’un [pipeline](data-factory-create-pipelines.md) Data Factory exécute des requêtes pig sur [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster ou cluster [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight. Cet article s'appuie sur l’article [Activités de transformation des données](data-factory-data-transformation-activities.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

## Syntaxe

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
	        	"inputs": [
	          		{
	            		"name": "input tables"
	          		}
	        	],
	        	"outputs": [
	          		{
	            		"name": "output tables"
	          		}
        		],
	        	"linkedServiceName": "MyHDInsightLinkedService",
	        	"typeProperties": {
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## Détails de la syntaxe

Propriété | Description | Requis
-------- | ----------- | --------
name | Nom de l’activité | Oui
description | Texte décrivant la raison motivant l’activité. | Non
type | HDinsightPig | Oui
inputs | Entrée(s) utilisée(s) par l'activité pig | Non
outputs | Sortie(s) produite(s) par l'activité pig | Oui
linkedServiceName | Référence au cluster HDInsight enregistré comme un service lié dans Data Factory | Oui
script | Spécifier le script en ligne pig | Non
chemin d'accès du script | Stockez le script pig dans un stockage d'objets blob Azure et indiquez le chemin d'accès au fichier. Utilisez la propriété ’script’ ou ’scriptPath’. Ces deux éléments ne peuvent pas être utilisés ensemble | Non
defines | Spécifier les paramètres sous forme de paires clé/valeur pour le référencement au sein du script pig | Non

## Exemple

Prenons un exemple d'analyse de journaux de jeux où vous souhaitez identifier le temps passé par les utilisateurs à jouer à des jeux créés par votre entreprise.
 
Voici un exemple de journal de jeu séparé par des virgules (,) et contenant les champs suivants : ProfileID, SessionStart, Duration, SrcIPAddress et GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

Le **script pig** pour traiter ces données se présente comme suit :

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Pour exécuter ce script pig dans un pipeline Data Factory, vous devez effectuer les opérations suivantes :

1. Créez un service lié pour inscrire [votre propre cluster de calcul HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurer un [cluster de calcul HDInsight à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Appelons ce service lié « HDInsightLinkedService ».
2.	Créez un [service lié](data-factory-azure-storage-connector.md) pour configurer la connexion au stockage d'objets blob Azure qui héberge les données. Appelons ce service lié « StorageLinkedService ».
3.	Créez des [jeux de données](data-factory-create-datasets.md) pointant vers les données d'entrée et de sortie. Appelons le jeu de données d'entrée « PigSampleIn » et le jeu de données de sortie « PigSampleOut ».
4.	Copiez la requête pig en tant que fichier vers le stockage d'objets blob Azure configuré à l'étape 2 ci-dessus. Si le service lié pour l'hébergement des données est différent de celui qui héberge ce fichier de requête, créez un service de stockage Azure lié distinct et référencez-le dans la configuration de l'activité. Utilisez **scriptPath** pour spécifier le chemin d'accès au fichier de requête pig et à **scriptLinkedService** afin de spécifier le stockage Azure qui contient le fichier de script.
	
	> [AZURE.NOTE]Vous pouvez également fournir le script en ligne pig dans la définition d'activité à l'aide de la propriété **script**, mais cela n'est pas recommandé car tous les caractères spéciaux du script au sein du document JSON doivent être placés dans une séquence d'échappement, ce qui risque d’entraîner des problèmes de débogage. La meilleure pratique consiste à suivre l’étape 4.
5. Créez le pipeline ci-dessous avec l'activité HDInsightPig pour traiter les données.

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. Déployez le pipeline. Consultez l’article [Création de pipelines](data-factory-create-pipelines.md) pour plus de détails. 
7. Surveillez le pipeline à l'aide des vues de gestion et de surveillance Data Factory. Consultez l’article [Surveillance et gestion des pipelines Data Factory](data-factory-monitor-manage-pipelines.md) pour plus d'informations.

## Spécification des paramètres d’un script pig à l’aide de l’élément defines

Prenons l'exemple où des journaux de jeux sont reçus quotidiennement dans le stockage blob Azure et conservés dans un dossier partitionné par date et par heure. Vous souhaitez paramétrer le script pig et fournir dynamiquement l'emplacement du dossier d'entrée pendant l'exécution mais aussi produire la sortie partitionnée par date et par heure.
 
Pour paramétrer le script pig, procédez comme suit :

- Définissez les paramètres dans **defines**.

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- Dans le script pig, reportez-vous aux paramètres à l'aide de ’**$parameterName**’ comme indiqué dans l'exemple suivant :

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 


 
## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-pig-activity.md).

<!---HONumber=Oct15_HO4-->