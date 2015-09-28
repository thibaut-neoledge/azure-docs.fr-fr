<properties
	pageTitle="Concevez votre premier pipeline Azure Data Factory en utilisant Data Factory Editor"
	description="Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l'aide de Data Factory Editor dans le portail Azure."
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
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Concevez votre premier pipeline Azure Data Factory en utilisant Data Factory Editor (portail Azure)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Dans cet article, vous apprendrez à utiliser le [portail Azure en version préliminaire](https://portal.azure.com/) pour créer votre premier pipeline. Ce didacticiel se déroule comme suit :

1.	Création de la fabrique de données
2.	Création des services liés (magasins de données, calculs) et des jeux de données
3.	Création du pipeline

Cet article ne fournit pas une vue d'ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, consultez l'article [Présentation d’Azure Data Factory](data-factory-introduction.md).

## Étape 1 : création de la fabrique de données

1.	Après la connexion au [portail Azure en version préliminaire](http://portal.azure.com/), procédez comme suit :
	1.	Cliquez sur **NOUVEAU**dans le menu de gauche. 
	2.	Cliquez sur **Analyse des données** dans le panneau **Créer**.
	3.	Cliquez sur **Data Factory** dans le panneau **Analyse des données**.

		![Panneau Créer](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Dans le panneau **Nouvelle fabrique de données**, saisissez **DataFactoryMyFirstPipeline** en guise de nom.

	![Panneau Nouvelle fabrique de données](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] Les noms Azure Data Factory sont globalement uniques. Vous devez faire précéder le nom de la fabrique de données par votre nom, pour activer la création de la fabrique.
3.	Si vous n’avez pas créé de groupe de ressources, vous devez en créer un. Pour ce faire :
	1.	Cliquez sur **NOM DU GROUPE DE RESSOURCES**.
	2.	Sélectionnez **Créer un groupe de ressources** dans le panneau **Groupe de ressources**.
	3.	Saisissez **ADF** pour le **Nom** dans le panneau **Créer un groupe de ressources**.
	4.	Cliquez sur **OK**.
	
		![Créer un groupe de ressources](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	Après avoir sélectionné le groupe de ressources, vérifiez que vous utilisez l’abonnement correspondant à celui dans lequel vous souhaitez créer la fabrique de données.
5.	Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.
6.	La fabrique de données apparaît comme étant en cours de création dans le **Tableau d'accueil** du portail Azure en version préliminaire, comme suit :   

	![État de la création de la fabrique de données](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Félicitations ! Vous avez créé votre première fabrique de données. Une fois la fabrique de données créée, vous verrez la page correspondante indiquant son contenu. 	

	![Panneau Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Dans les étapes suivantes, vous apprendrez à créer les services liés, les jeux de données et le pipeline que vous utiliserez dans ce didacticiel.

## Étape 2 : création des services et des jeux de données liés
Dans cette étape, vous lierez votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Vous créerez également un jeu de données pour représenter les données de sortie issues d’un traitement Hive.

### Créer le service lié Azure Storage
1.	Cliquez sur **Créer et déployer** dans le panneau **DATA FACTORY** pour **DataFactoryFirstPipeline**. Cette action lance l'éditeur Data Factory Editor. 
	 
	![Vignette Créer et déployer](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Cliquez sur **Nouveau magasin de données** et choisissez **Stockage Azure**
	
	![Service lié Azure Storage](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Le script JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans l’éditeur. 
4. Remplacez **account name** par le nom de votre compte de stockage Azure et **account key** par sa clé d'accès. Pour découvrir comment obtenir votre clé d'accès, consultez la rubrique [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md/#view-copy-and-regenerate-storage-access-keys)
5. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

	![Bouton déployer](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Création du service lié Azure HDInsight
Vous allez maintenant créer un service lié pour le cluster HDInsight à la demande qui sera utilisé pour exécuter le script Hive.

1. Dans **Data Factory Editor**, cliquez sur **Nouveau calcul** dans la barre de commandes et sélectionnez l’option **Cluster HDInsight à la demande**.

	![Nouveau calcul](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copiez et collez l'extrait ci-dessous dans la fenêtre Draft-1. L'extrait de code JSON décrit les propriétés permettant de créer le cluster HDInsight à la demande. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	Le tableau suivant décrit les propriétés JSON utilisées dans l'extrait de code :
	
	Propriété | Description
	-------- | -----------
	Version | Cette propriété indique que la version du service HDInsight doit être la version 3.1. 
	ClusterSize | Cette propriété crée un cluster HDInsight avec un seul nœud. 
	TimeToLive | Cette propriété spécifie la durée d'inactivité du cluster HDInsight, avant sa suppression.
	JobsContainer | Cette propriété spécifie le nom du conteneur de tâche qui sera créé pour stocker les journaux générés par HDInsight.
	linkedServiceName | Cette propriété spécifie le compte de stockage qui sera utilisé pour stocker les journaux générés par HDInsight.
3. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié. 
4. Confirmez que vous voyez s’afficher StorageLinkedService et HDInsightOnDemandLinkedService dans l’arborescence à gauche de l’écran.

	![Arborescence avec les services liés](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Créer le jeu de données de sortie
Vous allez maintenant créer le jeu de données de sortie pour représenter les données stockées dans le stockage Azure Blob.

1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commandes, puis sélectionnez **Stockage d’objets Blob Azure**.  

	![Nouveau jeu de données](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copiez et collez l'extrait ci-dessous dans la fenêtre Draft-1. Dans l’extrait de code JSON, vous créez un jeu de données appelé **AzureBlobOutput** et vous spécifiez la structure de données qui sera générée par le script Hive. En outre, vous spécifiez que les résultats sont stockés dans le conteneur d'objets blob appelé **données** et dans le dossier appelé **partitioneddata**. La section **disponibilité** spécifie que le jeu de données de sortie est généré sur une base mensuelle.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
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

3. Cliquez sur **Déployer** dans la barre de commandes pour déployer le jeu de données que vous venez de créer.
4. Vérifiez que le jeu de données a été correctement créé.

	![Arborescence avec les services liés](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Étape 3 : création de votre premier pipeline
Dans cette étape, vous allez créer votre premier pipeline.

1. Dans **Data Factory Editor**, cliquez sur **les points de suspension (…)** puis cliquez sur **Nouveau pipeline**.
	
	![bouton Nouveau pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copiez et collez l'extrait ci-dessous dans la fenêtre Draft-1.

	> [AZURE.IMPORTANT]Remplacez **storageaccountname** dans le script JSON par le nom de votre compte de stockage.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
 
	Dans l'extrait de code JSON, vous créez un pipeline qui se compose d'une seule activité utilisant Hive pour traiter des données sur un cluster HDInsight.
	
	Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le scriptLinkedService, appelé **StorageLinkedService**) et dans un conteneur appelé **script**.

	La section **extendedProperties** permet de spécifier les paramètres d'exécution qui seront transmis au script Hive en tant que valeurs de configuration Hive (par ex. ${hiveconf:PartitionedData}).

	Les propriétés **start** et **end** du pipeline spécifient la période active du pipeline.

	Dans l'activité JSON, vous spécifiez que le script Hive s'exécute sur le calcul spécifié par le service lié **HDInsightOnDemandLinkedService**.
3. Cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.
4. Vérifiez que le pipeline apparaît dans l'arborescence.

	![Arborescence avec pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Félicitations ! Vous avez créé votre premier pipeline !
6. Cliquez sur **X** pour fermer les panneaux du Data Factory Editor et pour revenir au panneau Data Factory, puis cliquez sur**Schéma**.
  
	![Vignette schématique](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. Dans la vue schématique, une vue d'ensemble des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche.
	
	![Vue schématique](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Dans la vue schématique, double-cliquez sur le jeu de données **AzureBlobOutput**. Le segment est en cours de traitement.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Lorsque le traitement est terminé, l’état du segment devient **Ready**. Notez que la création d'un cluster HDInsight à la demande prend généralement un certain temps. 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Lorsque le segment indique l’état **Prêt**, vérifiez le dossier **partitioneddata** du conteneur de **données** de votre stockage d'objets blob pour les données de sortie.  
 

 

## Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster HDInsight à la demande. Pour apprendre à utiliser une activité de copie pour copier des données à partir d'un objet blob Azure dans Azure SQL, consultez le [didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](./data-factory-get-started.md).
  

## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-editor.md).

<!---HONumber=Sept15_HO3-->