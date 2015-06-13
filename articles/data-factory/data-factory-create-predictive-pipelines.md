<properties 
	pageTitle="Data Factory - Créer des pipelines prédictifs à l'aide de Data Factory et Machine Learning | Azure" 
	description="Describes how to create create predictive pipelines using Azuer Data Factory and Azure Machine Learning" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Créer des pipelines prédictifs à l'aide d'Azure Data Factory et Azure Machine Learning 
## Vue d'ensemble
Vous pouvez configurer publié [Azure Machine Learning][azure-machine-learning] modèles au sein des pipelines de fabrique de données Azure. Ces pipelines sont appelés pipelines prédictives. Pour créer un pipeline prédictif, vous avez besoin des éléments suivants :

-	clé API et URL de notation par lots du modèle d'espace de travail publié (voir l'image ci-dessous) ;
-	Un blob Azure contenant le fichier CSV d'entrée (ou) une base de données SQL Azure qui contient l'entrée de données de stockage pour un score. 
-	Un stockage d'objets blob Azure qui contiendra le fichier CSV de score des résultats (ou) une base de données SQL Azure qui contiendra les données de sortie. 

	![Tableau de bord de formation de l'ordinateur][machine-learning-dashboard]

	Le lot score d'URL pour le AzureMLLinkedService proviennent comme indiqué dans l'image ci-dessus, moins ' ** aide ** » : https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Un **pipeline prédictive** comprend les éléments suivants :

-	tables d'entrée et de sortie ;
-	Azure Storage/Azure SQL et Azure ML liés de services
-	pipeline avec activité de notation par lots Azure ML.

> [AZURE.NOTE]Vous pouvez utiliser les paramètres de service Web qui sont exposées par un service Web de formation de Machine Azure publié dans les pipelines de la fabrique de données Azure (ADF). Pour plus d'informations, consultez la section Paramètres de Service Web dans cet article.

## Exemple
Cet exemple utilise le stockage Azure pour stocker les données d'entrée et de sortie. Vous pouvez également utiliser la base de données SQL Azure au lieu d'utiliser le stockage Azure.

Nous vous recommandons de suivre les [prise en main Azure Data Factory][adf-getstarted] didacticiel avant de passer par l'intermédiaire de cet exemple et l'utilisation de l'éditeur de fabrique de données pour créer des artefacts de fabrique de données (services liés, tables, pipeline) dans cet exemple.
 

1. Créez un service lié pour votre service Azure Storage. Si les fichiers d'entrée et de sortie de notation sont dans des comptes de stockage distincts, vous aurez besoin de deux services liés. Voici un exemple JSON :

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Créez les tables d'entrée et de sortie Azure Data Factory. Notez que contrairement à certains autres tables de la fabrique de données, celles-ci doivent contenir les deux **folderPath** et **fileName** valeurs. Vous pouvez utiliser le partitionnement et provoquer l'exécution de chaque lot (chaque tranche de données) pour traiter ou produire des fichiers d'entrée et de sortie uniques. Vous aurez probablement besoin d'inclure une activité en amont pour convertir l'entrée au format de fichier CSV, et la placer dans le compte de stockage pour chaque tranche. Dans ce cas, n'incluez pas les paramètres « waitOnExternal » indiqués dans l'exemple ci-dessous. Par ailleurs, ScoringInputBlob représente la table de sortie d'une autre activité.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}
	
	Votre lot score du fichier csv doit avoir la ligne d'en-tête de colonne. Si vous utilisez la **activité copie** pour créer ou le déplacement du volume partagé de cluster dans le stockage blob, vous devez définir la propriété de récepteur **blobWriterAddHeader** à **true**. Par exemple :
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si le fichier csv ne dispose pas de la ligne d'en-tête, vous pouvez rencontrer l'erreur suivante : **erreur dans l'activité : erreur de lecture de chaîne. Jeton inattendu : StartObject. Chemin d'accès », la ligne 1, la position 1**.
3. Cet exemple de sortie utilise le partitionnement pour créer un chemin de sortie unique à chaque exécution de tranche. Sinon, l'activité remplace le fichier.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Créer un service de type lié : **AzureMLLinkedService**, à condition que la clé d'API et le modèle par lots URL score.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Enfin, créez un pipeline contenant un **AzureMLBatchScoringActivity**. Il obtient l'emplacement du fichier d'entrée de vos tables d'entrée, appelle l'API de notation par lots AzureML, puis copie la sortie de notation par lots dans l'objet blob spécifié de votre table de sortie. Contrairement à d'autres activités Data Factory, AzureMLBatchScoringActivity ne peut avoir qu'une seule table d'entrée et de sortie.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}


## Paramètres de Service Web
Vous pouvez utiliser les paramètres de service Web qui sont exposées par un service Web de formation de Machine Azure publié dans les pipelines de la fabrique de données Azure (ADF). Vous pouvez créer une expérience d'apprentissage de Machine Azure publier sous la forme d'un service web et ensuite utiliser ce service web dans plusieurs pipelines ADF ou activités, en passant différentes entrées via les paramètres de Service Web.

### En passant les valeurs des paramètres de service Web
Ajouter un **transformation** section à la **AzureMLBatchScoringActivty** section dans le pipeline de JSON pour spécifier les valeurs des paramètres de service Web dans cette section, comme illustré dans l'exemple suivant :

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Vous pouvez également utiliser [données fabrique fonctions](https://msdn.microsoft.com/library/dn835056.aspx) en passant des valeurs pour le Web service paramètres comme indiqué dans l'exemple suivant :

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Les paramètres de service Web respectent la casse, assurez-vous que les noms que vous spécifiez dans l'activité JSON correspondent à celles exposées par le service Web.

### Enregistreurs et lecteurs SQL azure
Un scénario courant pour l'utilisation de paramètres de service Web est l'utilisation de lecteurs de SQL Azure et de Writers. Le module de lecture est utilisé pour charger des données dans une expérience à partir des services de gestion des données en dehors d'Azure Machine Learning Studio et le module de writer pour enregistrer les données à partir de vos expériences dans les services de gestion en dehors d'Azure Machine Learning Studio. Pour plus d'informations sur Azure Blob/Azure SQL lecture/écriture, consultez [lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) rubriques sur MSDN Library. L'exemple de la section précédente utilisé le lecteur d'objets Blob Azure et le writer d'objets Blob Azure. Cette section décrit l'utilisation du lecteur de SQL Azure et SQL Azure writer.

#### Lecteur SQL Azure
Dans Azure ML Studio, vous pouvez créer une expérience et publier un service Web avec un lecteur de SQL Azure pour l'entrée. Le lecteur de SQL Azure a des propriétés de connexion peuvent être exposées en tant que paramètres de service Web, autorisant les valeurs pour les propriétés de connexion doit être passé au runtime dans le traitement de demande de score.

Lors de l'exécution, les détails de la table d'entrée de la fabrique de données seront utilisés par le service Factory de données pour remplir les paramètres de service Web. Notez que vous devez utiliser des noms par défaut (nom du serveur de base de données, nom de la base de données, nom de compte utilisateur de serveur, mot de passe utilisateur Server) pour les paramètres de service Web pour l'intégration avec le service de fabrique de données fonctionne.

Si vous avez tout Web supplémentaire de paramètres de service, utilisez la **webServiceParameters** section de l'activité de JSON. Si vous spécifiez des valeurs pour les paramètres du lecteur de SQL Azure dans cette section, les valeurs seront substituent aux valeurs récupérés à partir du service SQL Azure lié d'entrée. Il est déconseillé de que spécifier de valeurs pour le lecteur de SQL Azure directement dans la section webServiceParameters. Utilisez la section pour passer des valeurs de paramètres supplémentaires.

Pour utiliser un lecteur de SQL Azure via un pipeline de fabrique de données Azure, procédez comme suit :

- Créer un **SQL Azure lié service**. 
- Créer une fabrique de données **table** qui utilise **AzureSqlTableLocation**.
- Définir cette fabrique de données **table** comme le **d'entrée** pour la **AzureMLBatchScoringActivity** dans le pipeline de JSON. 



#### Enregistreur SQL Azure
En tant que lecteur de SQL Azure, un enregistreur de SQL Azure peuvent également avoir ses propriétés exposées en tant que paramètres de service Web. Un enregistreur de SQL Azure utilise des paramètres à partir du service lié associé à la table d'entrée ou de la table de sortie. Le tableau suivant décrit quand l'entrée service lié est utilisé et sortie lié à service.

<table>
<tr>
<td>Sortie/entrée</td>
<td><b>L'entrée est SQL Azure</b></td>
<td><b>L'entrée est l'objet Blob Azure</b></td>
</tr>
<tr>
<td><b>Sortie est SQL Azure</b></td>
<td><p>Le service de fabrique de données utilise les informations de chaîne de connexion à partir du service d'entrée liée pour générer les paramètres de service web avec des noms: « Nom du serveur de base de données », « Nom de la base de données », « Nom du compte utilisateur Server », « Mot de passe de compte utilisateur Server ». Notez que vous devez utiliser ces noms par défaut pour les paramètres de service Web dans Azure ML Studio.</p>
<p>Si le lecteur de SQL Azure et Azure SQL Writer dans votre modèle ML Azure partagent les mêmes paramètres de service Web mentionnés ci-dessus, vous conviennent. Si elles ne partagent pas même paramers de service Web, par exemple, si l'enregistreur de SQL Azure utilise des noms de paramètres : bases de données serveur name1, name1 de base de données, name1 de compte utilisateur de serveur, utilisateur du serveur compte password1 (avec « 1 » à la fin), vous devez passer les valeurs de ces paramètres de sortie web service dans la section webServiceParameters d'activité JSON.</p>
<p>
Vous pouvez passer des valeurs pour d'autres paramètres de service Web à l'aide de la section webServiceParameters d'activité JSON.  
</p>

</td>
<td>
<p>Le service de fabrique de données utilise les informations de chaîne de connexion à partir du service de sortie lié pour générer les paramètres de service web avec des noms: « Nom du serveur de base de données », « Nom de la base de données », « Nom du compte utilisateur Server », « Mot de passe de compte utilisateur Server ». Notez que vous devez utiliser ces noms par défaut pour les paramètres de service Web dans Azure ML Studio.</p>
<p>Vous pouvez passer des valeurs pour d'autres paramètres de service Web à l'aide de la section webServiceParameters d'activité JSON. <p>Objet blob en entrée sera utilisé comme emplacement d'entrée.</p>
</td>
</tr>
<tr>
<td><b>Sortie est Blob Azure</b></td>
<td>Le service de fabrique de données utilise les informations de chaîne de connexion à partir du service d'entrée liée pour générer les paramètres de service web avec des noms: « Nom du serveur de base de données », « Nom de la base de données », « Nom du compte utilisateur Server », « Mot de passe de compte utilisateur Server ». Notez que vous devez utiliser ces noms par défaut pour les paramètres de service Web dans Azure ML Studio.
</td>
<td>
<p>Vous devez passer les valeurs des paramètres de service Web à l'aide de la section WebServiceParameters d'activité JSON.</p> 

<p>Objets BLOB sera utilisé comme emplacements d'entrée et de sortie.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Enregistreur de SQL Azure peut rencontrer des violations de clé si elle remplace une colonne d'identité. Vous devez vous assurer que vous structurez votre table de sortie pour éviter cette situation.
> 
> Vous pouvez utiliser des tables intermédiaires avec une activité de procédure stockée pour fusionner des lignes ou de tronquer les données avant de calculer les scores. Si vous utilisez cette approche, les paramètres d'accès concurrentiel de la stratégie d'exécution la valeur 1.

### Exemple d'utilisation de paramètres de service Web
#### Pipeline avec AzureMLBatchScoringActivity avec les paramètres de Service Web

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	]
		}
	}
 
Dans l'exemple JSON ci-dessus :

- Le modèle de licence de gestion Azure utilise Azure SQL lecteur et Writer de SQL Azure
- Lorsqu'elle est exposée via un service Web, les noms par défaut sont utilisées pour les paramètres
	- Pour le **lecteur**: nom du serveur, nom de la base de données, le nom de compte d'utilisateur serveur et mot de passe utilisateur serveur de base de données.
	- Pour le **writer**: base de données serveur name1, name1 de base de données, name1 de compte utilisateur de serveur et password1 de compte utilisateur de serveur.
	
		Notez que le lecteur et writer ne partagent pas les paramètres dans ce cas.  
- Le service de fabrique de données génère automatiquement les valeurs des paramètres de service Web avec les noms **nom du serveur de base de données**, **nom de base de données**, **nom du compte utilisateur Server**, et **mot de passe utilisateur Server**, qui correspondent aux noms du lecteur d'entrée. Par conséquent, vous n'avez pas besoin passer explicitement les valeurs de ces paramètres via **webServiceParameters** dans l'activité JSON ci-dessous.  
- Les paramètres de writer (celles avec le suffixe « 1 ») ne sont pas remplis automatiquement par le service de fabrique de données. Par conséquent, vous devez spécifier les valeurs de ces paramètres dans le **webServiceParameters** section de l'activité de JSON.  
- **Code client**, **un score étiquettes**, et **un score de probabilités** sont enregistrés sous forme de colonnes séparées par des virgules. 
- Le **nom de table de données** dans cet exemple correspond à une table dans la base de données de sortie.




## Voir aussi

Article | Description
------ | ---------------
[Référence du développeur fabrique données Azure][developer-reference] | La référence du développeur possède le contenu de référence complet pour les applets de commande, script JSON, bibliothèque de classes .NET, les fonctions, etc.... 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

<!---HONumber=GIT-SubDir--> 