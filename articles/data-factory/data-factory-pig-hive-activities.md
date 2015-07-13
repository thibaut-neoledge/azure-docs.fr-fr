<properties 
	pageTitle="Utilisation de Pig et Hive avec Azure Data Factory" 
	description="Découvrez comment traiter les données en exécutant des scripts Pig et Hive sur un cluster Azure HDInsight à partir d'une fabrique de données Azure." 
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
	ms.date="06/19/2015" 
	ms.author="spelluru"/>

# Utilisation de Pig et Hive avec Data Factory
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l’utilisation de l’activité HDInsight avec une transformation Pig/Hive dans un pipeline Azure Data Factory. Consultez la page [Appel de programmes MapReduce à partir de Data Factory][data-factory-map-reduce] pour plus d’informations sur l’exécution de programmes MapReduce sur un cluster HDInsight à partir d’un pipeline de fabrique de données Azure.

## Procédure pas à pas : utilisation de Hive avec Azure Data Factory
Cette procédure pas à pas fournit des instructions détaillées pour l’utilisation d’une activité HDInsight avec transformation Hive dans un pipeline Data Factory.

### Conditions préalables
1. Suivez le didacticiel de l’article [Prise en main d’Azure Data Factory][adfgetstarted].
2. Créez le fichier **hivequery.hql** dans un sous-dossier nommé **Hive** sous **C:\ADFGetStarted** avec le contenu suivant.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;

	> [AZURE.NOTE]Pour utiliser le moteur **Tez** afin d’exécuter des requêtes Hive dans le fichier HQL, ajoutez « **set hive.execution.engine=tez**; » en haut du fichier.
		
3.  Téléchargez **hivequery.hql** dans le conteneur **adftutorial** de votre stockage d’objets blob.


### Procédure pas à pas

#### Créer la table de sortie
        
1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données**, puis sur **Stockage d’objets blob Azure** dans la barre de commandes.
2. Remplacez le script JSON dans le volet droit par le script JSON suivant :

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Cliquez sur **Déployer** dans la barre de commandes pour déployer la table.


### Création d'un service lié pour un cluster HDInsight
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Le cluster HDInsight à la demande est automatiquement créé et géré par le service Azure Data Factory pour traiter les données. Consultez [Service lié à la demande Azure HDInishgt](https://msdn.microsoft.com/library/dn893526.aspx) pour plus d'informations sur le service lié à la demande HDInsight. Pour les besoins de l’exemple, nous allons utiliser un cluster à la demande. Notez que la création du cluster HDInsight à la demande prend au moins 15 minutes et que vous ne serez facturé que lorsque le cluster HDInsight sera opérationnel et exécutera des tâches.

#### Pour utiliser un cluster HDInsight à la demande
1. Cliquez sur l’option **Nouveau calcul** de la barre de commandes et sélectionnez **Cluster HDInsight à la demande** dans le menu.
2. Dans le script JSON, procédez comme suit : 
	1. Pour la propriété **clusterSize**, spécifiez la taille du cluster HDInsight.
	2. Pour la propriété **jobsContainer**, spécifiez le nom du conteneur par défaut dans lequel les journaux de cluster seront stockés. Pour les besoins de ce didacticiel, spécifiez **adfjobscontainer**.
	3. Pour la propriété **timeToLive**, spécifiez la durée pendant laquelle le cluster peut être inactif avant d’être supprimé. 
	4. Pour la propriété **version**, spécifiez la version de HDInsight que vous souhaitez utiliser. Si vous excluez cette propriété, la version utilisée sera la plus récente.  
	5. Pour **linkedServiceName**, spécifiez le service lié **StorageLinkedService** que vous avez créé dans le didacticiel de prise en main. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.
   
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Cliquez sur l’option **Nouveau calcul** de la barre de commandes et sélectionnez **Cluster HDInsight** dans le menu.
2. Dans le script JSON, procédez comme suit : 
	1. Pour la propriété **clusterUri**, saisissez l’URL de votre cluster HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/.     
	2. Pour la propriété **UserName**, saisissez le nom d’utilisateur ayant accès au cluster HDInsight.
	3. Pour la propriété **Password**, spécifiez le mot de passe de l’utilisateur. 
	4. Pour la propriété **LinkedServiceName**, saisissez **StorageLinkedService**. Désigne le service lié que vous avez créé dans le didacticiel de prise en main. 

2. Cliquez sur **Déployer** dans la barre de commandes pour déployer le service lié.

### Création et planification du pipeline
   
1. Cliquez sur **Nouveau pipeline** dans la barre de commandes. Si vous ne voyez pas la commande, cliquez sur **... (points de suspension)** pour l’afficher. 
2. Remplacez le script JSON affiché dans le volet droit par le script JSON suivant. Si vous souhaitez utiliser votre propre cluster et si vous avez suivi les étapes permettant de créer le service lié **HDInsightLinkedService**, remplacez **HDInsightOnDemandLinkedService** par **HDInsightLinkedService** dans le JSON suivant. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]Remplacez la valeur **StartDateTime** par une date antérieure de trois jours à la date actuelle, et remplacez la valeur **EndDateTime** par la date actuelle. Les valeurs StartDateTime et EndDateTime doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. La table de sortie est planifiée pour être produite tous les jours, trois tranches seront donc produites.
	> 
	> Remplacez **votre compte de stockage** dans le script JSON par le nom de votre compte de stockage.
	
	Pour plus d’informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2. Cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.
4. Consultez la section [Surveillance des jeux de données et du pipeline][adfgetstartedmonitoring] de l’article [Prise en main de Data Factory][adfgetstarted]. 

	> [AZURE.NOTE]Dans le panneau **DÉTAILS SUR L’EXÉCUTION D’ACTIVITÉ** pour une tranche d’une table de sortie (sélectionnez la table de sortie -> sélectionnez une tranche -> sélectionnez une activité exécutée dans le portail), vous voyez des liens vers les journaux créés par le cluster HDInsight. Vous pouvez les consulter sur le portail lui-même ou les télécharger sur votre ordinateur.
  

## Exemple JSON Pig
Lorsque vous définissez une activité Pig ou Hive dans un pipeline JSON, la propriété **type** doit être définie sur : **HDInsightActivity**.

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Notez les points suivants :**
	
- Le **type** de l’activité a la valeur **HDInsightActivity**.
- **linkedServiceName** a la valeur **MyHDInsightLinkedService**. Consultez la section du service lié HDInsight ci-dessous pour plus d’informations sur la création d’un service HDInsight lié.
- Le **type** de la **transformation** est défini sur **Pig**.
- Vous pouvez spécifier le script Pig en ligne pour la propriété **script**, ou stocker des fichiers de script dans un stockage d’objets blob Azure, et faire référence au fichier à l’aide de la propriété **scriptPath**, traitée plus loin dans cet article. 
- Pour spécifier des paramètres pour le script Pig, utilisez les **extendedProperties**. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. 


## Exemple JSON Hive


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Notez les points suivants :**
	
- Le **type** de l’activité a la valeur **HDInsightActivity**.
- **linkedServiceName** a la valeur **MyHDInsightLinkedService**. 
- Le **type** de la **transformation** est défini sur **Hive**.
- Vous pouvez spécifier le script Hive en ligne pour la propriété **script**, ou stocker des fichiers de script dans un stockage d’objets blob Azure, et faire référence au fichier à l’aide de la propriété **scriptPath**, traitée plus loin dans cet article. 
- Pour spécifier des paramètres pour le script Hive, utilisez les **extendedProperties**. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. 

> [AZURE.NOTE]Pour plus d’informations sur les applets de commande, les schémas JSON et les propriétés du schéma, consultez la [référence du développeur](http://go.microsoft.com/fwlink/?LinkId=516908).


## Utilisation de scripts Pig et Hive dans l’activité HDInsight
Vous pouvez stocker les scripts Pig/Hive dans un stockage d'objets blob Azure associé au cluster HDInsight et y faire référence à partir d'activités Pig/Hive en utilisant les propriétés suivantes dans le script JSON :

* **scriptPath :** chemin d’accès vers le fichier de script Pig ou Hive
* **scriptLinkedService** : compte de stockage Azure qui contient le fichier de script

L’exemple de pipeline JSON suivant utilise une activité Hive qui fait référence au fichier **transformdata.hql** stocké dans le dossier **scripts** du conteneur **adfwalkthrough** dans le stockage d’objets blob Azure représenté par **StorageLinkedService**.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}


> [AZURE.NOTE]Pour utiliser le moteur **Tez** pour exécuter une requête Hive, exécutez « **set hive.execution.engine=tez**; » avant d’exécuter la requête Hive.
> 
> Pour plus d’informations sur les applets de commande, les schémas JSON et les propriétés du schéma, consultez la [référence du développeur](http://go.microsoft.com/fwlink/?LinkId=516908).

## Requêtes Pig et Hive paramétrables
Les activités Pig et Hive de Data Factory vous permettent de spécifier des valeurs pour les paramètres utilisés dans les scripts Pig et Hive en utilisant **extendedProperties**. La section extendedProperties se compose du nom et de la valeur du paramètre.

Consultez l’exemple suivant pour spécifier des paramètres pour un script Hive en utilisant **extendedProperties**. Pour utiliser des scripts Hive paramétrables, procédez comme suit :

1.	Définissez les paramètres dans **extendedProperties**.
2.	Dans le script Hive en ligne ou dans le fichier de script Hive stocké dans le stockage d’objets blob, faites référence au paramètre en utilisant **${hiveconf:parameterName}**.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


## Voir aussi

Article | Description
------ | ---------------
[Didacticiel : déplacement et traitement de fichiers journaux à l’aide de Data Factory][adf-tutorial] | Cet article comporte une procédure pas à pas de bout en bout qui montre comment implémenter un scénario réel à l’aide d’Azure Data Factory pour transformer des données de fichiers journaux en informations pertinentes.
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence pour les développeurs comportent des informations complètes sur les applets de commande, le script JSON, les fonctions, etc. 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=62-->