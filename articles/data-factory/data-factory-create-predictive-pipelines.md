<properties 
	pageTitle="Data Factory - Créer des pipelines prédictifs à l’aide de Data Factory et Machine Learning | Microsoft Azure"
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
	ms.date="08/04/2015"
	ms.author="spelluru"/>

# Créer des pipelines prédictifs à l'aide d'Azure Data Factory et Azure Machine Learning 
## Vue d'ensemble

Azure Data Factory vous permet de créer facilement des pipelines qui s'appuient sur un service web [Azure Machine Learning][azure-machine-learning] publié pour l'analyse prédictive. Cela vous permet d’utiliser Azure Data Factory pour orchestrer le mouvement et le traitement des données, puis d’effectuer une notation par lot à l'aide d'Azure Machine Learning. Pour ce faire, vous devez effectuer les opérations suivantes :

1. Utilisez l’activité **AzureMLBatchScoring**.
2. **URI de demande** pour l’API d’exécution de lot. Vous trouverez l'URI de demande en cliquant sur le lien **BATCH EXECUTION** dans la page des services web (voir ci-dessous).
3. **Clé API** pour le service web Azure Machine Learning publié. Vous trouverez ces informations en cliquant sur le service web que vous avez publié. 

	![Tableau de bord Machine Learning][machine-learning-dashboard]

Un **pipeline prédictif** se compose des éléments suivants :

-	Des tables d'entrée et de sortie
-	Des services liés Azure Storage/SQL Azure et Azure ML
-	Un pipeline avec activité de notation par lots Azure ML

> [AZURE.NOTE]Vous pouvez utiliser les paramètres de service web qui sont exposés par un service web Azure Machine Learning publié dans les pipelines Azure Data Factory (ADF). Pour plus d'informations, consultez la section Paramètres de service web dans cet article.

## Exemple
Cet exemple utilise Azure Storage pour stocker les données d'entrée et de sortie. Vous pouvez également utiliser Azure SQL Database à la place d'Azure Storage.

Nous vous recommandons de passer en revue le didacticiel [Concevez votre premier pipeline en utilisant Azure Data Factory][adf-build-1st-pipeline] avant de lire cet exemple et d’utiliser Data Factory Editor pour créer des artefacts Data Factory (services liés, tables, pipeline).
 

1. Créez un **service lié** pour votre service **Azure Storage**. Si les fichiers d'entrée et de sortie de notation sont dans des comptes de stockage distincts, vous aurez besoin de deux services liés. Voici un exemple JSON :

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Créez la **table** d'**entrée** Azure Data Factory. Notez que contrairement à d'autres tables Data Factory, celles-ci doivent contenir les valeurs **folderPath** et **fileName**. Vous pouvez utiliser le partitionnement et provoquer l'exécution de chaque lot (chaque tranche de données) pour traiter ou produire des fichiers d'entrée et de sortie uniques. Vous aurez probablement besoin d'inclure une activité en amont pour convertir l'entrée au format de fichier CSV, et la placer dans le compte de stockage pour chaque tranche. Dans ce cas, n'incluez pas les paramètres **external** et **externalData** indiqués dans l'exemple ci-dessous. Par ailleurs, ScoringInputBlob représente la table de sortie d'une autre activité.

		{
		  "name": "ScoringInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
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
	
	Votre fichier csv de notation par lots doit disposer d'une ligne d'en-tête de colonnes. Si vous utilisez l'**activité de copie** pour créer/déplacer le fichier .csv dans le stockage d'objets blob, vous devez définir la propriété du récepteur **blobWriterAddHeader** sur **true**. Par exemple :
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si le fichier csv ne dispose pas de ligne d'en-tête, l'erreur suivante risque de se produire : **Erreur pendant l'activité : erreur de lecture de la chaîne. Jeton inattendu : StartObject. Chemin d’accès ’’, ligne 1, position 1**.
3. Créez la **table** de **sortie** Azure Data Factory. Cet exemple utilise le partitionnement pour créer un chemin de sortie unique à chaque exécution de tranche. Sinon, l'activité remplace le fichier.

		{
		  "name": "ScoringResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Créez un **service lié** de type **AzureMLLinkedService** en fournissant la clé API et l'URL de notation par lots du modèle.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Enfin, créez un pipeline contenant **AzureMLBatchScoringActivity**. Il obtient l'emplacement du fichier d'entrée de vos tables d'entrée, appelle l'API de notation par lots AzureML, puis copie la sortie de notation par lots dans l'objet blob spécifié de votre table de sortie. Contrairement à d'autres activités Data Factory, AzureMLBatchScoringActivity ne peut avoir qu'une seule table d'entrée et de sortie.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchScoring",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "ScoringInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "ScoringResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}


## Paramètres de service web
Vous pouvez utiliser les paramètres de service web qui sont exposés par un service web Azure Machine Learning publié dans les pipelines Azure Data Factory (ADF). Vous pouvez créer une expérience dans Azure Machine Learning et la publier sous la forme d'un service web, puis utiliser ce service web dans plusieurs pipelines ou activités ADF, en transmettant différentes entrées via les paramètres de service web.

### Transmission de valeurs pour les paramètres de service web
Ajoutez une section **typeProperties** à la section **AzureMLBatchScoringActivty** dans le script JSON du pipeline pour spécifier les valeurs des paramètres de service web dans cette section, comme indiqué dans l’exemple suivant :

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Vous pouvez également utiliser les [fonctions de Data Factory](https://msdn.microsoft.com/library/dn835056.aspx) pour transmettre les valeurs aux paramètres de service web, comme indiqué dans l'exemple suivant :

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \'{0:yyyy-MM-dd HH:mm:ss}\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Les paramètres de service web respectent la casse ; veillez donc à ce que les noms que vous indiquez dans le script JSON de l'activité correspondent à ceux exposés par le service web.

### Modules Lecteur et Enregistreur

L'utilisation des paramètres de service web passe par un scénario commun : l'utilisation des lecteurs et enregistreurs SQL Azure. Le module Lecteur est utilisé pour charger des données dans une expérience, à partir des services de gestion des données, en dehors d'Azure Machine Learning Studio. Le module Enregistreur sauvegarde quant à lui les données de vos expériences dans les services de gestion des données, en dehors d'Azure Machine Learning Studio. Pour plus d'informations concernant les modules enregistreur/lecteur Azure Blob/SQL Azure, consultez les rubriques [Lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et [Enregistreur](https://msdn.microsoft.com/library/azure/dn905984.aspx) dans la bibliothèque MSDN. L'exemple de la section précédente utilisait le lecteur et l'enregistreur d'objets blob Azure. Cette section décrit leur utilisation.

#### SQL Azure comme source de données
Dans Azure ML Studio, vous pouvez créer une expérience et publier un service web avec un lecteur SQL Azure pour l'entrée. Le lecteur SQL Azure dispose de propriétés de connexion pouvant être exposées en tant que paramètres de service web, ce qui permet de transmettre les valeurs des propriétés de connexion dans la demande de notation par lots, pendant l'exécution.

Au moment de l'exécution, les détails provenant de la table d'entrée Data Factory sont utilisés par le service Data Factory pour remplir les paramètres de service web. Notez que vous devez utiliser les noms par défaut (Database server name, Database name, Server user account name, Server user account password) pour les paramètres de service web si vous souhaitez que l'intégration avec le service Data Factory fonctionne.

Si vous avez des paramètres de service web supplémentaires, utilisez la section **webServiceParameters** du script JSON de l'activité. Si vous spécifiez des valeurs pour les paramètres du lecteur SQL Azure dans cette section, les valeurs écrasent celles récupérées dans le service lié SQL Azure. Nous vous déconseillons de spécifier les valeurs du lecteur SQL Azure directement dans la section webServiceParameters. Utilisez la section pour transmettre les valeurs des paramètres supplémentaires.

Pour utiliser un lecteur SQL Azure via un pipeline Azure Data Factory, procédez comme suit :

- Créez un **service lié SQL Azure**. 
- Créez une **table** Data Factory qui utilise **AzureSqlTable**.
- Définissez cette **table** Data Factory comme **entrée** pour **AzureMLBatchScoringActivity** dans le script JSON du pipeline. 



#### SQL Azure comme un récepteur de données
Comme pour le lecteur SQL Azure, les propriétés de l'enregistreur SQL Azure peuvent également être exposées en tant que paramètres de service web. Un enregistreur SQL Azure utilise les paramètres du service lié associé à la table d'entrée ou à la table de sortie. Le tableau suivant décrit quand le service lié d'entrée est utilisé et quand celui de sortie l'est.

| Sortie/entrée | L'entrée est Azure SQL. | L'entrée est Azure Blob. |
| ------------ | ------------------ | ------------------- |
| La sortie est Azure SQL. | <p>Le service Data Factory utilise les informations de la chaîne de connexion du service lié INPUT pour générer les paramètres de service web avec les noms « Database server name », « Database name », « Server user account name » et « Server user account password ». Notez que vous devez utiliser ces noms par défaut pour les paramètres de service web dans Azure ML Studio.</p><p>Si le lecteur et l’enregistreur SQL Azure de votre modèle Azure ML partagent les mêmes paramètres de service web que ceux mentionnés ci-dessus, vous pouvez continuer. S’ils ne partagent pas les mêmes paramètres de service web, par exemple, si l’enregistreur SQL Azure utilise les noms de paramètre Database server name1, Database name1, Server user account name1, Server user account password1 (avec le chiffre « 1 » à la fin), vous devez transmettre les valeurs de ces paramètres de sortie du service web dans la section webServiceParameters de l’activité JSON.</p><p>Vous pouvez transmettre les valeurs des autres paramètres de service web en utilisant la section webServiceParameters de l’activité JSON.</p> | <p>Le service Data Factory utilise les informations de la chaîne de connexion du service lié de sortie pour générer les paramètres de service web avec les noms « Database server name », « Database name », « Server user account name » et « Server user account password ». Notez que vous devez utiliser ces noms par défaut pour les paramètres de service web dans Azure ML Studio.</p><p>Vous pouvez transmettre les valeurs des autres paramètres de service web à l’aide de la section webServiceParameters de l’activité JSON. <p>L’objet blob d’entrée est utilisé comme emplacement d’entrée.</p> |
|La sortie est Azure Blob | Le service Data Factory utilise les informations de la chaîne de connexion du service lié INPUT pour générer les paramètres de service web avec les noms « Database server name », « Database name », « Server user account name » et « Server user account password ». Notez que vous devez utiliser ces noms par défaut pour les paramètres de service web dans Azure ML Studio. | <p>Vous devez transmettre les valeurs des paramètres de service web en utilisant la section WebServiceParameters de l’activité JSON.</p><p>Les objets blob sont utilisés comme emplacements d’entrée et de sortie.</p> |
    

> [AZURE.NOTE]Si l'enregistreur SQL Azure écrase une colonne d'identité, sa clé risque de rencontrer des violations. Veillez donc à structurer votre table de sortie de manière à éviter cette situation.
> 
> Vous pouvez utiliser des tables intermédiaires avec une activité de procédure stockée pour fusionner des lignes ou tronquer les données avant de procéder à la notation. Si vous utilisez cette approche, réglez les paramètres d'accès concurrentiel d'executionPolicy sur la valeur 1.

#### Objet blob Azure comme source

Lorsque vous utilisez le module Lecteur d'une Azure Machine Learning, vous pouvez spécifier un objet blob Azure comme entrée. Les fichiers du stockage blob Azure peuvent être les fichiers de sortie (par exemple, 000000\_0) qui sont générés par un script Pig et Hive exécuté sur HDInsight. Le module Lecteur vous permet de lire des fichiers (sans extension) en configurant la propriété **Chemin d’accès au conteneur, répertoire ou blob** du module lecteur pour pointer vers le dossier/conteneur où figurent les fichiers, comme indiqué ci-dessous. Notez que l’astérisque (*) **spécifie que tous les fichiers du dossier/conteneur (par exemple, data/aggregateddata/year=2014/month-6/*)** seront lus dans le cadre de l’expérience.

![Propriétés des objets blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### Exemple d'utilisation des paramètres de service web
#### Pipeline avec AzureMLBatchScoringActivity avec les paramètres de service web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchScoring",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	        "typeProperties": {
	          "webServiceParameters": {
	            "Database server name1": "output.database.windows.net",
	            "Database name1": "outputDatabase",
	            "Server user account name1": "outputUser",
	            "Server user account password1": "outputPassword",
	            "Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
	            "Data table name": "BikeBuyerPredicted"
	          }
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
Dans l'exemple JSON ci-dessus :

- Le modèle Azure ML utilise le lecteur et l'enregistreur SQL Azure.
- Quand ils sont exposés via un service web, les noms par défaut sont utilisés pour les paramètres.
	- Pour le **lecteur** : Database server name, Database name, Server user account name et Server user account password.
	- Pour l'**enregistreur** : Database server name1, Database name1, Server user account name1 et Server user account password1.
	
		Notez que, dans ce cas, le lecteur et l'enregistreur ne partagent pas les mêmes paramètres.  
- Le service Data Factory génère automatiquement les valeurs des paramètres de service web avec les noms **Database server name**, **Database name**, **Server user account name** et **Server user account password**, qui correspondent aux noms du lecteur d'entrée. Par conséquent, vous n'avez pas besoin de transmettre explicitement les valeurs de ces paramètres via **webServiceParameters** dans le script JSON de l'activité ci-dessous.  
- Les paramètres de l'enregistreur (ceux avec le suffixe « 1 ») ne sont pas remplis automatiquement par le service Data Factory. Par conséquent, vous devez spécifier les valeurs de ces paramètres dans la section **webServiceParameters** du script JSON de l'activité.  
- **CustID**, **Scored Labels** et **Scored Probabilities** sont enregistrés sous forme de colonnes séparées par des virgules. 
- Dans cet exemple, **Data table name** correspond à une table dans la base de données de sortie.
- Les dates/heures de **début** et de **fin** doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de **fin** est facultative. Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour la propriété **end**. Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](https://msdn.microsoft.com/library/dn835050.aspx).





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=August15_HO9-->