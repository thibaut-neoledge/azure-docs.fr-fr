<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="Utilisation de Pig et Hive avec Azure Data Factory" description="Découvrez comment traiter les données en exécutant des scripts Pig et Hive sur un cluster Azure HDInsight à partir d'une fabrique de données Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Utilisation de Pig et Hive avec Data Factory
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue   une opération de traitement spécifique. Par exemple, une activité de type " Copie " copie les données depuis un stockage source vers un stockage de destination et une activité HDInsight avec des transformations Hive/Pig utilise un cluster Azure HDInsight pour traiter les données à l'aide de scripts Hive/Pig. L'activité HDInsight peut consommer une ou plusieurs entrées et produire une ou plusieurs sorties. 

## Dans cet article

Section | Description
------- | -----------
[Exemple JSON Pig](#PigJSON) | Cette section fournit un schéma JSON pour définir une activité HDInsight qui utilise une transformation Pig. 
[Exemple JSON Hive](#HiveJSON) | Cette section fournit un schéma JSON pour définir une activité HDInsight qui utilise une transformation Hive. 
[Utilisation de scripts Pig et Hive stockés dans le stockage d'objets blob Azure](#ScriptInBlob) | Décrit comment faire référence à des scripts Pig/Hive stockés dans un stockage d'objets blob Azure depuis une activité HDInsight à l'aide d'une transformation Pig/Hive.
[Requêtes Pig et Hive paramétrables](#ParameterizeQueries) | Décrit comment spécifier des valeurs pour les paramètres utilisés dans les scripts Pig et Hive, à l'aide de la propriété **extendedProperties** dans JSON.
[Procédure pas à pas : Utilisation de Hive avec Azure Data Factory](#Waltkthrough) | Fournit les instructions détaillées permettant de créer un pipeline qui utilise Hive pour traiter les données.  



Lorsque vous définissez une activité Pig ou Hive dans un pipeline JSON, la propriété **type** doit être définie sur : **HDInsightActivity**.

## <a name="PigJSON"></a> Exemple JSON Pig

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

**Notez les points suivants :**
	
- Le **type** de l'activité est défini sur **HDInsightActivity**.
- **linkedServiceName** est défini sur **MyHDInsightLinkedService**. 
- Le **type** de la **transformation** est défini sur **Pig**.
- Vous pouvez spécifier le script Pig en ligne pour la propriété **script** ou stocker des fichiers de script dans un stockage d'objets blob Azure et faire référence au fichier à l'aide de la propriété **scriptPath**, traitée plus loin dans cet article. 
- Pour spécifier des paramètres pour le script Pig, utilisez **extendedProperties**. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. 


## <a name="HiveJSON"></a> ## Exemple JSON Hive


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

**Notez les points suivants :**
	
- Le **type** de l'activité est défini sur **HDInsightActivity**.
- **linkedServiceName** est défini sur **MyHDInsightLinkedService**. 
- Le **type** de la **transformation** est défini sur **Hive**.
- Vous pouvez spécifier le script Hive en ligne pour la propriété **script** ou stocker des fichiers de script dans un stockage d'objets blob Azure et faire référence au fichier à l'aide de la propriété **scriptPath**, traitée plus loin dans cet article. 
- Pour spécifier des paramètres pour le script Hive, utilisez **extendedProperties**. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. 

> [WACOM.NOTE] Pour plus d'informations sur les cmdlets, les schémas JSON et les propriétés du schéma, consultez la rubrique [Référence du développeur](http://go.microsoft.com/fwlink/?LinkId=516908). 


## <a name="ScriptInBlob"></a>Utilisation de scripts Pig et Hive stockés dans le stockage d'objets blob Azure
Vous pouvez stocker les scripts Pig/Hive dans un stockage d'objets blob Azure associé au cluster HDInsight et y faire référence à partir d'activités Pig/Hive en utilisant les propriétés suivantes dans le script JSON : 

* **scriptPath** : chemin d'accès vers le fichier de script Pig ou Hive
* **scriptLinkedService** : compte de stockage Azure qui contient le fichier de script

L'exemple de pipeline JSON suivant utilise une activité Hive qui fait référence au fichier **transformdata.hql** stocké dans le dossier **scripts** du conteneur **adfwalkthrough** dans le stockage d'objets blob Azure représenté par **StorageLinkedService**.

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
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
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

  

> [WACOM.NOTE] Pour plus d'informations sur les cmdlets, les schémas JSON et les propriétés du schéma, consultez la rubrique [Référence du développeur](http://go.microsoft.com/fwlink/?LinkId=516908).

## <a name="ParameterizeQueries"></a>Requêtes Pig et Hive paramétrables
Pour les activités Pig et Hive de Data Factory, vous pouvez spécifier des valeurs pour les paramètres utilisés dans les scripts Pig et Hive en utilisant **extendedProperties**. La section extendedProperties se compose du nom et de la valeur du paramètre.

Reportez-vous à l'exemple suivant pour spécifier des paramètres pour un script Hive en utilisant **extendedProperties**. Pour utiliser des scripts Hive paramétrables  , procédez comme suit :

1.	Définissez les paramètres dans **extendedProperties**.
2.	Dans le script Hive en ligne ou dans le fichier de script Hive stocké dans le stockage d'objets blog, faites référence au paramètre en utilisant **${hiveconf:parameterName}**.

   
    		
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


-  

## <a name="Walkthrough"></a>Procédure pas à pas : utilisation de Hive avec Azure Data Factory
### Conditions préalables
1. Suivez le didacticiel de l'article [Prise en main d'Azure Data Factory][adfgetstarted].
2. Téléchargez le fichier **emp.txt** que vous avez créé dans le didacticiel précédent dans **hiveinput\emp.txt** dans le conteneur adftutorial du stockage d'objets blob. Le dossier **hiveinput** est automatiquement créé dans le conteneur **adftutorial** lorsque vous téléchargez le fichier emp.txt avec cette syntaxe. 
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
		
3.  Téléchargez **hivequery.hql** dans le conteneur **adftutorial** de votre stockage d'objets blob


### Procédure pas à pas

#### Créer une table d'entrée
1. Créez un fichier JSON nommé **HiveInputBlobTable.json** dans le dossier **C:\ADFGetStarted\Hive** avec le contenu suivant.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**Notez les points suivants :**
	
	- Le **type** d'emplacement a la valeur **AzureBlobLocation**.
	- **linkedServiceName** est défini sur **MyBlobStore** qui définit un compte de stockage Azure.
	- **folderPath** spécifie le conteneur d'objets blob/dossier des données d'entrée. 
	- **frequency=Day** et **interval=1** indiquent que les tranches sont disponibles quotidiennement.
	- **waitOnExternal** signifie que ces données ne sont pas produites par un autre pipeline, mais à l'extérieur de la fabrique de données. 
	

	Pour obtenir une description des propriétés JSON, consultez la rubrique [Référence du développeur Data Factory][developer-reference].  

2. Lancez **Azure PowerShell** et basculez en mode **AzureResourceManager** si besoin.
    		
    	Switch-AzureMode AzureResourceManager

5. Basculez vers le dossier : **C:\ADFGetStarted\Hive**.
6. Exécutez la commande suivante pour créer la table d'entrée dans **ADFTutorialDataFactory**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	Pour une présentation détaillée des cmdlets Data Factory, consultez la rubrique [Référence des cmdlets Azure Data Factory][cmdlet-reference]. 
#### Créer la table de sortie
        
1. Créez un fichier JSON nommé **HiveOutputBlobTable.json** avec le contenu suivant et enregistrez-le dans le dossier **C:\ADFGetStarted\Hive**.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Exécutez la commande suivante pour créer la table de sortie dans **ADFTutorialDataFactory**.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### Création d'un service lié pour un cluster HDInsight
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, lorsque vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l'exemple, nous allons utiliser un cluster à la demande. 

#### Pour utiliser un cluster HDInsight à la demande
1. Créez un fichier JSON nommé **HDInsightOnDemandCluster.json** avec le contenu suivant et enregistrez-le dans le dossier **C:\ADFGetStarted\Hive**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Lancez **Azure PowerShell** et exécutez la commande suivante pour passer au mode **AzureResourceManager**. Les cmdlets Azure Data Factory sont disponibles dans le mode **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Accédez au dossier **C:\ADFGetstarted\Hive**.
4. Exécutez la commande suivante pour créer le service lié pour le cluster HDInsight à la demande.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. Les tables et les services liés doivent s'afficher sur le panneau **Data Factory** de la **version préliminaire du portail Azure**.    
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Créez un fichier JSON nommé **MyHDInsightCluster.json** avec le contenu suivant et enregistrez-le dans le dossier **C:\ADFGetStarted\Hive**. Avant d'enregistrer le fichier JSON, remplacez clustername, username et password par les valeurs appropriées.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Lancez **Azure PowerShell** et exécutez la commande suivante pour passer en mode **AzureResourceManager**. Les cmdlets Azure Data Factory sont disponibles dans le mode **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Accédez au dossier **C:\ADFGetstarted\Hive**.
4. Exécutez la commande suivante pour créer le service lié pour votre propre cluster HDInsight.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### Création et planification du pipeline
   
1. Créez un fichier JSON nommé **ADFTutorialHivePipeline.json** avec le contenu suivant et enregistrez-le dans le dossier **C:\ADFGetStarted\Hive**. Si vous souhaitez utiliser votre propre cluster et que vous avez suivi les étapes permettant de créer le service lié **MyHDInsightCluster**, remplacez **HDInsightOnDemandCluster** par **MyHDInsightCluster** dans le script JSON suivant. 


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
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
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

2. Exécutez la commande suivante pour créer le pipeline.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. Planifiez le pipeline.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] Remplacez la valeur **StartDateTime** par la date correspondant à trois jours avant la date actuelle, et remplacez la valeur **EndDateTime** par la date actuelle. Les valeurs StartDateTime et EndDateTime sont exprimées en heure UTC et doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. 
	> Si vous ne spécifiez pas la valeur **EndDateTime**, elle est calculée comme suit : **StartDateTime + 48 heures**. Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme **EndDateTime**.
  	
	La table de sortie est planifiée pour être produite tous les jours, trois tranches seront donc produites. 

4. Consultez la section [Surveillance des jeux de données et du pipeline][adfgetstartedmonitoring] de l'article [Prise en main de Data Factory][adfgetstarted].   

## Voir aussi

Article | Description
------ | ---------------
[Présentation d'Azure Data Factory][data-factory-introduction] | Cet article présente le service Azure Data Factory, les concepts associés, sa valeur ajoutée et les scénarios qu'il prend en charge.
[Prise en main d'Azure Data Factory][adf-getstarted] | Cet article propose un didacticiel pas à pas qui permet de créer un exemple de fabrique de données Azure qui copie les données d'un objet blob Azure vers une base de données SQL Azure.
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas permettant de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline. 
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory.  
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les cmdlets, le script JSON, les fonctions, etc. 


[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Référence du développeur]: http://go.microsoft.com/fwlink/?LinkId=516908
[Portail Azure]: http://portal.azure.com
