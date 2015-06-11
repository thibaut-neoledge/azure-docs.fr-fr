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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Utilisation de Pig et Hive avec Data Factory
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l'utilisation de l'activité de HDInsight avec transformation Pig/Hive dans un pipeline de fabrique de données Azure. Voir [appeler des programmes MapReduce à partir de données fabrique][data-factory-map-reduce] pour plus d'informations sur l'exécution de MapReduce programmes sur un HDInsight cluster à partir d'un pipeline de fabrique de données Azure.

## Procédure pas à pas : Utilisation de Hive avec fabrique de données Azure
Cette procédure pas à pas fournit des instructions détaillées pour l'utilisation d'une activité de HDInsight avec transformation Hive dans un pipeline de fabrique de données.

### Conditions préalables
1. Suivre le didacticiel de [prise en main Azure Data Factory][adfgetstarted] l'article.
2. Télécharger **emp.txt** fichier que vous avez créé dans le didacticiel ci-dessus en tant que **hiveinput\\emp.txt** au conteneur adftutorial dans le stockage blob. Le **hiveinput** dossier est créé automatiquement dans le **adftutorial** conteneur lorsque vous téléchargez le fichier emp.txt avec cette syntaxe.

	> [AZURE.NOTE]Le fichier emp.txt est uniquement un fichier fictif pour cette procédure pas à pas. Les données d'entrée réelles proviennent de la **hivesampletable** qui existe déjà sur le cluster HDInsight. Le pipeline n'utilise pas le fichier emp.txt.
	
2. Créer **hivequery.hql** fichier dans un sous-dossier nommé **la ruche** sous **C:\ADFGetStarted** avec le contenu suivant.
    		
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

	> [AZURE.NOTE]Pour utiliser le **Tez** moteur pour exécuter des requêtes Hive dans le fichier HQL, ajoutez "** définir hive.execution.engine=tez**; » en haut du fichier.
		
3.  Télécharger le **hivequery.hql** à la **adftutorial** conteneur dans votre stockage d'objets blob


### Procédure pas à pas

#### Créer une table d'entrée
1. Dans le **DATA FACTORY** lame pour le **ADFTutorialDataFactory**, cliquez sur **auteur et déployer** pour lancer l'éditeur de fabrique de données.
	
	![Fabrique lame][data-factory-blade]

2. Dans le **éditeur Data Factory**, cliquez sur **nouveau dataset**, puis cliquez sur **stockage d'objets Blob Azure** à partir de la barre de commandes.
3. Remplacez le script JSON dans le volet droit par le script JSON suivant :    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
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
	
	- emplacement **type** a **AzureBlobLocation**.
	- **linkedServiceName** a **StorageLinkedService** qui définit un compte de stockage Azure.
	- **folderPath** spécifie les container\\folder d'objets blob pour les données d'entrée. 
	- **fréquence = jour** et **intervalle = 1** signifie que les tranches sont disponibles, tous les jours
	- **waitOnExternal** signifie que ces données ne sont pas produites par un autre pipeline est plutôt généré en externe à la fabrication de données. 
	

	Voir [référence du développeur données fabrique][developer-reference] pour obtenir une description des propriétés JSON.

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le tableau.
  
#### Créer la table de sortie
        
1. Dans le **éditeur Data Factory**, cliquez sur **nouveau dataset**, puis cliquez sur **stockage d'objets Blob Azure** à partir de la barre de commandes.
2. Remplacez le script JSON dans le volet droit par le script JSON suivant :

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

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le tableau.


### Création d'un service lié pour un cluster HDInsight
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, lorsque vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l'exemple, nous allons utiliser un cluster à la demande.

#### Pour utiliser un cluster HDInsight à la demande
1. Cliquez sur **Nouveau calcul** à partir de la barre de commandes et sélectionnez **cluster HDInsight sur demande** dans le menu.
2. Procédez comme suit dans le script JSON : 
	1. Pour le **clusterSize** propriété, spécifiez la taille du cluster HDInsight.
	2. Pour le **jobsContainer** propriété, spécifiez le nom du conteneur par défaut où les journaux de cluster seront stockés. Pour les besoins de ce didacticiel, spécifiez **adfjobscontainer**.
	3. Pour le **timeToLive** propriété, spécifiez la durée pendant laquelle le cluster peut être inactif avant d'être supprimé. 
	4. Pour le **version** propriété, spécifiez la version de HDInsight que vous souhaitez utiliser. Si vous excluez cette propriété, la dernière version est utilisée.  
	5. Pour le **linkedServiceName**, spécifiez **StorageLinkedService** que vous avez créé à la méthode Get de démarrer le didacticiel. 

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

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le service lié.
   
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Cliquez sur **Nouveau calcul** à partir de la barre de commandes et sélectionnez **cluster HDInsight** à partir du menu.
2. Procédez comme suit dans le script JSON : 
	1. Pour le **clusterUri** propriété, entrez l'URL de votre HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/     
	2. Pour le **nom d'utilisateur** propriété, entrez le nom d'utilisateur qui a accès au cluster HDInsight.
	3. Pour le **mot de passe** propriété, entrez le mot de passe de l'utilisateur. 
	4. Pour le **LinkedServiceName** propriété, entrez **StorageLinkedService**. Désigne le service lié que vous avez créé dans le didacticiel démarré Get. 

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le service lié.

### Création et planification du pipeline
   
1. Cliquez sur **nouveau pipeline** sur la barre de commandes. Si vous ne voyez pas la commande, cliquez sur **... (Sélection)** pour l'afficher. 
2. Remplacez le JSON dans le volet droit par le script JSON suivant. Si vous souhaitez utiliser votre propre cluster et suivi les étapes pour créer le **HDInsightLinkedService** lié service, remplacez **HDInsightOnDemandLinkedService** avec **HDInsightLinkedService** dans le JSON suivant. 


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
						"linkedServiceName": "HDInsightLinkedService",
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
		                    "scriptpath": "adftutorial\\hivequery.hql",
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

	> [AZURE.NOTE]Remplacez **StartDateTime** valeur avec les trois jours précédant la date du jour et **EndDateTime** valeur avec la date du jour. StartDateTime et EndDateTime doivent être dans [au format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. La table de sortie est planifiée pour être produite tous les jours, trois tranches seront donc produites.
	
	> [AZURE.NOTE]Remplacez **votre compte de stockage** dans JSON avec le nom de votre compte de stockage.
	
	Voir [référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) pour plus d'informations sur les propriétés JSON.
2. Cliquez sur **déployer** sur la barre de commandes pour déployer le pipeline.
4. Voir [surveiller les datasets et pipeline][adfgetstartedmonitoring] section de [prise en main Data Factory][adfgetstarted] l'article. 

	> [AZURE.NOTE]Dans le **Détails de l'exécution activité** lame pour une tranche d'une table de sortie (sélectionnez la table de sortie -> sélectionnez Tranche -> sélectionnez une activité exécutée dans le portail), vous verrez des liens vers les journaux créés par le cluster HDInsight. Vous pouvez les consulter dans le portail lui-même ou les télécharger sur votre ordinateur.
  

## Exemple JSON Pig
Lorsque vous définissez une activité Pig ou Hive dans un pipeline de JSON, le **type** propriété doit être définie sur : **HDInsightActivity**.

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
	
- Activité **type** a **HDInsightActivity**.
- **linkedServiceName** a **MyHDInsightLinkedService**. Consultez la section service HDInsight lié ci-dessous pour plus d'informations sur la création d'un service HDInsight lié.
- Le **type** de la **transformation** est définie sur **Pig**.
- Vous pouvez spécifier Pig script inline pour le **script** propriété ou magasin de fichiers de script dans Azure, stockage d'objets blob et faire référence au fichier à l'aide **scriptPath** propriété, qui est expliquée plus loin dans cet article. 
- Vous spécifiez les paramètres du script Pig à l'aide de la **extendedProperties**. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. 


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

**Notez les points suivants :**
	
- Activité **type** a **HDInsightActivity**.
- **linkedServiceName** a **MyHDInsightLinkedService**. 
- Le **type** de la **transformation** est définie sur **la ruche**.
- Vous pouvez spécifier la ruche de script inline pour le **script** propriété ou magasin de fichiers de script dans Azure, stockage d'objets blob et faire référence au fichier à l'aide **scriptPath** propriété, qui est expliquée plus loin dans cet article. 
- Vous spécifiez les paramètres du script Hive à l'aide de la **extendedProperties**. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. 

> [AZURE.NOTE]Voir [référence du développeur](http://go.microsoft.com/fwlink/?LinkId=516908) pour plus d'informations sur les applets de commande, les schémas JSON et les propriétés dans le schéma.


## À l'aide de scripts Pig et Hive dans l'activité de HDInsight
Vous pouvez stocker les scripts Pig/Hive dans un stockage d'objets blob Azure associé au cluster HDInsight et y faire référence à partir d'activités Pig/Hive en utilisant les propriétés suivantes dans le script JSON :

* **scriptPath** – chemin d'accès au fichier de script Pig ou Hive
* **scriptLinkedService** – compte de stockage Azure qui contient le fichier de script

L'exemple suivant de JSON pour un exemple de pipeline utilise une activité Hive qui fait référence à **transformdata.hql** fichier stocké dans **scripts** dossier dans le **adfwalkthrough** conteneur dans le stockage blob Azure représenté par le **StorageLinkedService**.

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


> [AZURE.NOTE]Pour utiliser le **Tez** moteur pour exécuter une requête Hive, exécutez "** définir hive.execution.engine=tez**; » avant d'exécuter la requête Hive.
> 
> Voir [référence du développeur](http://go.microsoft.com/fwlink/?LinkId=516908) pour plus d'informations sur les applets de commande, les schémas JSON et les propriétés dans le schéma.

## Requêtes Pig et Hive paramétrables
Les activités de données fabrique Pig et Hive permettent de spécifier des valeurs pour les paramètres utilisés dans les scripts Pig et Hive, à l'aide de **extendedProperties**. La section extendedProperties se compose du nom et de la valeur du paramètre.

Consultez l'exemple suivant pour spécifier les paramètres d'un script de la ruche à l'aide **extendedProperties**. Pour utiliser des scripts Hive paramétrables, procédez comme suit :

1.	Définir les paramètres dans **extendedProperties**.
2.	Dans le script de ruche en ligne (ou) fichier de script Hive stockées dans le stockage d'objets BLOB, reportez-vous à l'à l'aide du paramètre **${hiveconf:parameterName}**.

   
    		
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
[Didacticiel : Déplacer et traiter des fichiers journaux à l'aide de la fabrique de données][adf-tutorial] | Cet article fournit une procédure pas à pas bout en bout qui montre comment implémenter une véritable proche à l'aide de la fabrique de données Azure pour transformer les données des fichiers journaux en insights le cas.
[Référence du développeur fabrique données Azure][developer-reference] | La référence du développeur possède le contenu de référence complet pour les applets de commande, script JSON, fonctions, etc.... 

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

<!---HONumber=GIT-SubDir--> 