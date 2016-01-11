<properties
	pageTitle="Prise en main d’Azure Data Factory (Portail Azure)"
	description="Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l’aide de Data Factory Editor dans le portail Azure."
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
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Prise en main d’Azure Data Factory (Data Factory Editor)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)

Dans cet article, vous allez utiliser le [portail Azure](https://portal.azure.com/) pour créer votre première fabrique de données Azure.

## Conditions préalables

1. Vous **devez** lire l’article [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et effectuer les étapes préalables avant de continuer.
2. Cet article ne fournit pas de vue d’ensemble conceptuelle du service Azure Data Factory. Nous vous recommandons de lire l’article [Introduction à Azure Data Factory](data-factory-introduction.md) pour une présentation détaillée du service.  

## Étape 1 : Créer la fabrique de données
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline peut contenir une ou plusieurs activités. Par exemple, une activité de copie pour copier des données d’une source vers un magasin de données de destination, et une activité Hive HDInsight pour exécuter un script Hive pour transformer des données d’entrée et produire des données de sortie. Commençons par la création de la fabrique de données dans cette étape.

1.	Une fois connecté au [portail Azure](http://portal.azure.com/), procédez comme suit :
	1.	Cliquez sur **NOUVEAU** dans le menu de gauche. 
	2.	Cliquez sur **Analyse des données** dans le panneau **Créer**.
	3.	Cliquez sur **Fabrique de données** dans le panneau **Analyse des données**.

		![Panneau Créer](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Dans le panneau **Nouvelle fabrique de données**, entrez **GetStartedDF** dans le champ Nom.

	![Panneau Nouvelle fabrique de données](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche : **Le nom de la fabrique de données « GetStartedDF » n’est pas disponible**, changez le nom de la fabrique de données (par exemple en votrenomGetStartedDF), puis réessayez de la créer. Consultez la rubrique [Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
	>  
	> Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.

3.	Sélectionnez l’**abonnement Azure** où vous voulez que la fabrique de données soit créée.
4.	Sélectionnez un **groupe de ressources** existant ou créez-en un. Pour les besoins de ce didacticiel, créez un groupe de ressources nommé : **ADFGetStartedRG**.    
5.	Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.
6.	La fabrique de données apparaît comme étant en cours de création dans le **Tableau d’accueil** du portail Azure comme suit :   

	![État de la création de la fabrique de données](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Félicitations ! Vous avez créé votre première fabrique de données. Une fois la fabrique de données créée, vous verrez la page correspondante indiquant son contenu. 	

	![Panneau Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Avant de créer un pipeline, vous devez d’abord créer quelques entités de la fabrique de données. Créez d’abord des services liés pour lier des magasins de données/calculs à votre magasin de données, définissez des jeux de données d’entrée et de sortie pour représenter les données dans les magasins de données liés, puis créez le pipeline avec une activité qui utilise ces jeux de données.

## Étape 2 : Créer des services liés
Dans cette étape, vous allez lier votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Le compte de stockage Azure contient les données d’entrée et de sortie pour le pipeline de cet exemple. Le service lié HDInsight est utilisé pour exécuter le script Hive spécifié dans l’activité du pipeline de cet exemple. Vous devez identifier les services de magasin de données/de calcul qui sont utilisés dans votre scénario et les lier à la fabrique de données en créant des services liés.

### Créer le service lié Azure Storage
Dans cette étape, vous allez lier votre compte de stockage Azure à votre fabrique de données. Pour les besoins de ce didacticiel, vous utilisez le même compte de stockage Azure pour stocker les données d’entrée/sortie et le fichier de script HQL.

1.	Cliquez sur **Créer et déployer** dans le panneau **FABRIQUE DE DONNÉES** pour **GetStartedDF**. Cette action lance Data Factory Editor. 
	 
	![Vignette Créer et déployer](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Cliquez sur **Nouveau magasin de données** et choisissez **Stockage Azure**
	
	![Service lié Azure Storage](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Le script JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans l’éditeur. 
4. Remplacez **account name** par le nom de votre compte de stockage Azure et **account key** par sa clé d’accès. Pour découvrir comment obtenir votre clé d’accès de stockage, consultez [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
5. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

	![Bouton déployer](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Une fois que le service lié est déployé, la fenêtre **Draft-1** doit disparaître tandis que **StorageLinkedService** doit apparaître dans l’arborescence sur la gauche. 
   	![Service lié au stockage dans le menu](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)

 
### Créer le service lié Azure HDInsight
Dans cette étape, vous allez lier un cluster HDInsight à la demande à votre fabrique de données. Le cluster HDInsight est automatiquement créé lors de l’exécution, puis supprimé une fois le traitement effectué et au terme du délai d’inactivité spécifié. Vous pouvez utiliser votre propre cluster HDInsight au lieu d’utiliser un cluster HDInsight à la demande. Pour plus d’informations, consultez [Services de calcul liés](data-factory-compute-linked-services.md).

1. Dans **Data Factory Editor**, cliquez sur **Nouveau calcul** dans la barre de commandes et sélectionnez l’option **Cluster HDInsight à la demande**.

	![Nouveau calcul](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copiez et collez l’extrait ci-dessous dans la fenêtre **Draft-1**. L'extrait de code JSON décrit les propriétés permettant de créer le cluster HDInsight à la demande. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	Le tableau suivant décrit les propriétés JSON utilisées dans l'extrait de code :
	
	| Propriété | Description |
	| :------- | :---------- |
	| Version | Cette propriété indique que la version de service HDInsight doit être la version 3.2. | 
	| ClusterSize | Cette propriété crée un cluster HDInsight avec un seul nœud. | 
	| TimeToLive | Cette propriété spécifie la durée d'inactivité du cluster HDInsight, avant sa suppression. |
	| linkedServiceName | Cette propriété spécifie le compte de stockage qui sera utilisé pour stocker les journaux générés par HDInsight. |
3. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié. 
4. Confirmez que vous voyez s’afficher **StorageLinkedService** et **HDInsightOnDemandLinkedService** dans l’arborescence à gauche de l’écran.

	![Arborescence avec les services liés](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## Étape 3 : Créer les jeux de données
Dans cette étape, vous allez créer des jeux de données pour représenter les données d’entrée et de sortie pour le traitement Hive. Ces jeux de données font référence au service **StorageLinkedService** que vous avez créé précédemment dans ce didacticiel. Le service lié pointe vers un compte de stockage Azure, et les jeux de données spécifient le conteneur, le dossier et le nom de fichier dans le stockage qui contient les données d’entrée et de sortie.

### Créer la table d’entrée

1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commandes, puis sélectionnez **Stockage d’objets Blob Azure**.

	![Nouveau jeu de données](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copiez et collez l’extrait ci-dessous dans la fenêtre Draft-1. Dans l’extrait de code JSON, vous créez un jeu de données appelé **AzureBlobInput**, qui représente les données d’entrée pour une activité dans le pipeline. En outre, vous spécifiez que les données d’entrée sont stockées dans le conteneur d’objets blob appelé **adfgetstarted** et dans le dossier appelé **inputdata**.
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	Le tableau suivant décrit les propriétés JSON utilisées dans l’extrait de code :

	| Propriété | Description |
	| :------- | :---------- |
	| type | La propriété type est définie sur AzureBlob, car les données se trouvent dans le stockage d’objets blob Azure. |  
	| linkedServiceName | fait référence au service StorageLinkedService que vous avez créé précédemment. |
	| fileName | Cette propriété est facultative. Si vous omettez cette propriété, tous les fichiers spécifiés dans le paramètre folderPath sont récupérés. Dans le cas présent, seul le fichier input.log est traité. |
	| type | Les fichiers journaux sont au format texte : nous allons donc utiliser TextFormat. | 
	| columnDelimiter | Les colonnes des fichiers journaux sont délimitées par « , » (virgule) |
	| frequency/interval | La fréquence est définie sur Mois et l’intervalle est 1, ce qui signifie que les segments d’entrée sont disponibles mensuellement. | 
	| external | Cette propriété a la valeur true si les données d’entrée ne sont pas générées par le service Data Factory. | 
	  
	
3. Cliquez sur **Déployer** dans la barre de commandes pour déployer le jeu de données que vous venez de créer. Vous devez voir le jeu de données dans l’arborescence sur la gauche.


### Créer le jeu de données de sortie
Vous allez maintenant créer le jeu de données de sortie pour représenter les données de sortie stockées dans le stockage d’objets blob Azure.

1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commandes, puis sélectionnez **Stockage d’objets Blob Azure**.  
2. Copiez et collez l’extrait ci-dessous dans la fenêtre Draft-1. Dans l’extrait de code JSON, créez un jeu de données appelé **AzureBlobOutput** et spécifiez la structure des données qui seront produites par le script Hive. Indiquez aussi que les résultats sont stockés dans le conteneur d’objets blob appelé **adfgetstarted** et dans le dossier appelé **partitioneddata**. La section **availability** spécifie que le jeu de données de sortie est produit tous les mois.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
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
		}

	Consultez la section **Créer le jeu de données d’entrée** pour obtenir une description de ces propriétés. Vous ne définissez pas la propriété externe sur un jeu de données de sortie, car le jeu de données est produit par le service Data Factory.
3. Cliquez sur **Déployer** dans la barre de commandes pour déployer le jeu de données que vous venez de créer.
4. Vérifiez que le jeu de données a été correctement créé.

	![Arborescence avec les services liés](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Étape 4 : créer votre premier pipeline
Dans cette étape, vous allez créer votre premier pipeline avec une activité **HDInsightHive**. Notez que le segment d’entrée est disponible mensuellement (fréquence : Mois, intervalle : 1), que le segment de sortie est produit mensuellement et que la propriété du planificateur pour l’activité est également définie sur Mensuellement (voir ci-dessous). Les paramètres pour le jeu de données de sortie et le planificateur d’activité doivent correspondre. À ce stade, le jeu de données de sortie est ce qui pilote la planification : vous devez donc créer un jeu de données de sortie même si l’activité ne génère aucune sortie. Si l’activité ne prend aucune entrée, vous pouvez ignorer la création du jeu de données d’entrée. Les propriétés utilisées dans le code JSON suivant sont expliquées à la fin de cette section.

1. Dans **Data Factory Editor**, cliquez sur **les points de suspension (…)**, puis cliquez sur **Nouveau pipeline**.
	
	![bouton Nouveau pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copiez et collez l’extrait ci-dessous dans la fenêtre Draft-1.

	> [AZURE.IMPORTANT]Dans le code JSON, remplacez **storageaccountname** par le nom de votre compte de stockage.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}
 
	Dans l’extrait de code JSON, créez un pipeline qui se compose d’une seule activité utilisant Hive pour traiter des données sur un cluster HDInsight.
	
	Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le service scriptLinkedService, appelé **StorageLinkedService**) et dans un dossier **script** du conteneur **adfgetstarted**.

	La section **defines** est utilisée pour spécifier les paramètres d’exécution qui seront passés au script Hive comme valeurs de configuration Hive (par exemple ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Les propriétés **start** et **end** du pipeline spécifient la période active du pipeline.

	Dans l’activité JSON, vous spécifiez que le script Hive s’exécute sur le calcul spécifié par le service **linkedServiceName** – **HDInsightOnDemandLinkedService**.


3. Vérifiez que le fichier input.log apparaît dans le dossier adfgetstarted/inputdata du stockage d’objets blob Azure, puis cliquez sur **Déployer** sur la barre de commandes pour déployer le pipeline. Étant donné que les valeurs pour **start** et **end ** sont définies sur des valeurs antérieures au moment actuel, et que **isPaused** est défini sur false, le pipeline (activité dans le pipeline) s’exécute immédiatement après le déploiement.
4. Vérifiez que le pipeline apparaît dans l’arborescence.

	![Arborescence avec pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Félicitations ! Vous avez créé votre premier pipeline !

## Étape 4 : Surveiller le pipeline

6. Cliquez sur **X** pour fermer les panneaux du Data Factory Editor et revenir au panneau Data Factory, puis cliquez sur **Diagramme**.
  
	![Vignette de diagramme](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. Dans la vue de diagramme, une présentation des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche.
	
	![Vue de diagramme](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Pour afficher toutes les activités du pipeline, cliquez avec le bouton droit sur le pipeline dans le diagramme, puis cliquez sur Ouvrir un pipeline. 

	![Menu Ouvrir un pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Vérifiez que l’activité HDInsightHive est bien dans le pipeline. 
  
	![Vue Ouvrir un pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

	Pour revenir à la vue précédente, cliquez sur **Fabrique de données** dans le menu de navigation du haut. 
10. Dans la **Vue de diagramme**, double-cliquez sur le jeu de données **AzureBlobInput**. Vérifiez que l’état du segment est **Prêt**. Plusieurs minutes peuvent être nécessaires avant que le segment n’apparaisse avec l’état Prêt. Si rien ne se produit au bout d’un moment, vérifiez que le fichier d’entrée (input.log) est placé dans le conteneur (adfgetstarted) et le dossier (inputdata) appropriés.

	![Segment d’entrée dans l’état Prêt](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Cliquez sur **X** pour fermer le panneau **AzureBlobInput**. 
12. Dans la **Vue de diagramme**, double-cliquez sur le jeu de données **AzureBlobOutput**. Le segment est en cours de traitement.

	![Jeu de données](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Quand le traitement est terminé, l’état du segment devient **Prêt**.
	>[AZURE.IMPORTANT]La création d’un cluster HDInsight à la demande prend généralement un certain temps (environ 20 minutes).  

	![Jeu de données](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)
	
10. Quand l’état du segment est **Prêt**, vérifiez la présence des données de sortie dans le dossier **partitioneddata** du conteneur **adfgetstarted** de votre stockage d’objets blob.
 
	![données de sortie](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

## Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster HDInsight à la demande. Pour voir comment utiliser une activité de copie pour copier des données depuis un objet blob Azure vers Azure SQL, consultez le [Didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](./data-factory-get-started.md).

### Références
| Rubrique | Description |
| :---- | :---- |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory et comment les utiliser pour créer des flux de travail pilotés par les données de bout en bout pour votre scénario ou votre entreprise. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article va vous aider à comprendre les jeux de données dans Azure Data Factory.
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md) | Dans cet article, vous apprendrez à surveiller, gérer et déboguer vos pipelines. Vous obtiendrez également des informations sur la façon de créer des alertes et être averti en cas d’échec. |


  

<!---HONumber=AcomDC_1223_2015-->