<properties 
	pageTitle="Azure Data Factory Editor" 
	description="Décrit Azure Data Factory Editor, qui vous permet de créer, modifier et déployer des définitions JSON de services liés, de tables et de pipelines à l’aide d’une interface utilisateur web légère." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Azure Data Factory Editor
Azure Data Factory Editor est un éditeur web léger appartenant au portail Azure en version préliminaire, qui vous permet de créer, modifier et déployer des fichiers JSON de toutes les entités Azure Data Factory. Il vous permet de créer des services liés, des jeux de données et des pipelines en utilisant les modèles JSON fournis avec le service Data Factory. Si vous êtes un nouvel utilisateur de PowerShell, cet éditeur vous évite l’installation et le passage par Azure PowerShell pour créer des fabriques de données Azure.

## Démarrage de Data Factory Editor
Pour démarrer Data Factory Editor, cliquez sur la vignette **Créer et déployer** sur le volet **Data Factory** de votre fabrique de données Azure.

![Vignette Créer et déployer][author-and-deploy-tile]

Data Factory Editor devrait s’afficher comme suit :
 
![Data Factory Editor][data=factory-editor]
 
## Création de services liés, de jeux de données et de pipelines
La barre d'outils comporte quatre boutons, permettant de créer des entités Azure Data Factory.
 
- **Nouveau magasin de données** : permet de créer un service de magasin de données liées. Lorsque vous cliquez sur ce bouton, un menu s’affiche en présentant les options suivantes : stockage Azure, base de données SQL Azure, base de données SQL Server locale.
- **Nouveau calcul** : permet de créer un service de calcul lié. Lorsque vous cliquez sur ce bouton, un menu s’affiche en présentant les options suivantes : cluster HDInsight à la demande, cluster HDInsight, service AzureML lié.      
- **Nouveau jeu de données** : permet de créer un jeu de données. Lorsque vous cliquez sur ce bouton, les options suivantes s’affichent : table d’objet blob, table Azure SQL, table locale.  
- **Nouveau pipeline** : permet de créer un pipeline. Si ce bouton n’apparaît pas dans la barre d’outils, cliquez sur **... (points de suspension)**.
 
### Création d'un service de stockage lié
1. Cliquez sur **Nouveau magasin de données**, puis sur une des options du menu.
 
	![Menu Nouveau magasin de données][new-data-store-menu] 
2. Le modèle JSON pour la création d'un service de stockage lié s’affiche dans la **zone Éditeur** sur la droite. Vous verrez également qu'un nœud de brouillon apparaît sous **Brouillons**. Effectuez les actions suivantes :
	1. Pour **Stockage Azure** : remplacez **<accountname>** et **<accountkey>** par le nom et la clé de votre compte de stockage Azure.
	2. Pour **Base de données SQL Azure** : remplacez **<servername>** par le nom de votre serveur SQL Azure, **<databasename>** par le nom de la base de données, **<username>@<servername>** par le nom de l'utilisateur et **<password>** par le mot de passe du compte d'utilisateur. 
	3. Pour **Base de données SQL Server locale** : remplacez **<servername>** par le nom de votre serveur SQL Azure, **<databasename>** par le nom de la base de données, **<username>** par le nom de l'utilisateur et **<password>** par le mot de passe du compte d'utilisateur.
4. Cliquez sur **Déployer** sur la barre d'outils pour déployer le service lié. Vous pouvez cliquer sur **Ignorer** pour supprimer le brouillon JSON que vous avez créé.
 
	![Bouton déployer][deploy-button]

1. L'état de l'opération de déploiement doit s’afficher sur la barre de titre.

	![Message Déploiement réussi][deploy-success-message]
2. Si l'opération de déploiement réussit, le service lié créé s’affiche dans l'arborescence, sur la gauche.
  
	![StorageLinkedService dans l’arborescence][storagelinkedservice-in-treview]

### Création d'un service de calcul lié
1. Cliquez sur **Nouveau calcul**, puis sur une des options du menu.
 
	![Menu Nouveau calcul][new-compute-menu] 
2. Le modèle JSON pour la création d'un service de calcul lié s’affiche dans la zone Éditeur sur la droite. Effectuez les actions suivantes :
	1. Pour **Cluster HDInsight à la demande**, spécifiez des valeurs pour les propriétés suivantes : 
		1. Pour la propriété **clusterSize**, indiquez la taille du cluster HDInsight que le service Data Factory doit créer lors de l'exécution. 
		2. Pour **jobsContainer**, spécifiez le nom du conteneur d'objets blob par défaut où vous souhaitez stocker les journaux du cluster.
		3. Pour la propriété **timeToLive**, spécifiez la durée d'inactivité autorisée pour le cluster HDInsight avant sa suppression. Par exemple : 00:05:00 indique que le cluster doit être supprimé après 5 minutes d'inactivité.
		4. Pour la propriété **version**, spécifiez la version de HDInsight pour le cluster (par défaut : version 3.1).
		5. Pour la propriété **linkedServiceName**, spécifiez le service de stockage Azure lié à associer avec le cluster HDInsight. 
	6. Pour **Cluster HDInsight** (BYO), spécifiez les valeurs des propriétés suivantes :
		1. Pour la propriété **clusterUri**, spécifiez l'URL de votre cluster HDInsight. 
		2. Pour la propriété **userName**, spécifiez le compte d'utilisateur que le service Data Factory doit utiliser pour se connecter à votre cluster HDInsight. 
		3. Pour la propriété **password**, spécifiez le mot de passe du compte d’utilisateur. 
		4. Pour la propriété **linkedServiceName**, spécifiez le service de stockage Azure lié associé à votre cluster HDInsight.
	5. Pour **Service AzureML lié**, spécifiez les propriétés suivantes :
		1. Pour la propriété **mlEndPoint**, spécifiez l’URL de notation par lot du service Azure Machine Learning.
		2. Pour la propriété **apiKey**, spécifiez la clé d'API du modèle d'espace de travail publié.
3. Cliquez sur **Déployer** sur la barre d'outils pour déployer le service lié.

> [AZURE.NOTE]Consultez la rubrique [Services liés][msdn-linkedservices-reference] dans la bibliothèque MSDN pour obtenir une description des éléments JSON qui servent à définir un service lié Azure Data Factory.

### Création d’un jeu de données
1. Cliquez sur **Nouveau jeu de données**, puis sur une des options du menu.
2. Le modèle JSON pour la création d'un jeu de données lié s’affiche dans la zone Éditeur sur la droite. Effectuez les actions suivantes : 
	1. Pour **Table d’objets blob**, spécifiez des valeurs pour les propriétés suivantes :
	2. Pour **Table SQL Azure** ou **Table locale**, spécifiez des valeurs pour les propriétés suivantes : 
		1. Dans la section **emplacement** : 
			2. Pour la propriété **linkedServiceName**, spécifiez le nom du service lié pointant vers votre base de données Azure SQL/SQL Server locale.
			2. Pour la propriété **tableName**, spécifiez le nom de la table dans l’instance de base de données Azure SQL/SQL Server locale vers laquelle pointe le service lié.
		3. Dans la section **disponibilité** :
			1. Pour la propriété **frequency**, spécifiez l'unité de temps pour la production de tranches de données. Les valeurs prises en charge pour le paramètre frequency sont : Minute, Hour, Day, Week, Month (minute, heure, jour, semaine, mois).
			2. Pour la propriété **interval**, spécifiez l'intervalle de la fréquence définie. Si le paramètre **frequency** est défini sur **Hour** et que le paramètre **interval** est défini sur **1**, cela indique que les nouvelles tranches de données doivent être générées toutes les heures. 
		3. Dans la section **structure** : 
			1. Spécifiez les noms et les types de colonnes, comme illustré dans l'exemple suivant :
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]Consultez la rubrique [Tables][msdn-tables-reference] dans la bibliothèque MSDN pour obtenir une description des éléments JSON qui servent à définir une table Azure Data Factory.
 		           
### Création et activation d’un pipeline 
1. Cliquez sur **Nouveau pipeline** sur la barre d'outils. Si le nouveau bouton **Nouveau pipeline** ne s’affiche pas, cliquez sur **... (points de suspension)** pour l'afficher.   
2. Le modèle JSON pour la création d'un pipeline s’affiche dans la zone Éditeur sur la droite. Effectuez les actions suivantes : 
	1. Pour la propriété **description**, entrez une description du pipeline.
	2. Pour la section **activités**, ajoutez des activités au pipeline. Exemple :
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
					"type": "CopyActivity",
					"inputs": [ {"name": "EmpTableFromBlob"} ],
					"outputs": [ {"name": "EmpSQLTable"} ],		
					"transformation":
					{
						"source":
						{                               
							"type": "BlobSource"
						},
						"sink":
						{
							"type": "SqlSink"
						}	
					},
					"Policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"style": "StartOfInterval",
						"retry": 0,
						"timeout": "01:00:00"
					}		
				}
    		]
	3. Pour la propriété **start**, spécifiez le début du traitement des données ou du traitement des tranches de données. Par exemple : 2014-05-01T00:00:00Z.
	4. Pour la propriété **end**, spécifiez la fin du traitement des données. Par exemple : 2014-05-01T00:00:00Z.       

> [AZURE.NOTE]Consultez la rubrique [Pipelines et activités][msdn-pipelines-reference] dans la bibliothèque MSDN pour obtenir une description des éléments JSON qui servent à définir un pipeline Azure Data Factory.

## Ajout d’une définition d’activité à un pipeline JSON
Vous pouvez ajouter une définition d'activité à un pipeline JSON en cliquant sur **Ajouter une activité** sur la barre d'outils. Lorsque vous cliquez sur ce bouton, vous choisissez le type d'activité que vous souhaitez ajouter au pipeline.

![Options Ajouter une activité][add-activity-options]

Si vous souhaitez copier les données d'une base de données SQL Azure pour le stockage d'objets blob Azure et traiter les données dans le stockage d’objets blob à l'aide d’un script Pig sur un cluster HDInsight, vous devez commencer par ajouter une **activité Copy**, puis une **activité Pig** au pipeline. Cela crée deux sections dans la section Activités du pipeline JSON. Une activité pig n’est rien d'autre qu’une activité HDInsight dotée d’une transformation Pig.

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## Démarrage d'un pipeline
Vous pouvez spécifier la date et l’heure de début et de fin d’un pipeline en spécifiant des valeurs pour les propriétés start et end du JSON.

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## Brouillons dans l'éditeur
Brouillons vous permet d'enregistrer temporairement votre travail en cas de changement de contexte ou lorsque vous accédez à une autre entité de Data Factory. La durée de vie des brouillons est associée à la session de navigateur. Si vous fermez le navigateur ou utilisez un autre ordinateur, les brouillons ne seront plus disponibles.

## Suppression d’un brouillon JSON d'une entité Data Factory
Vous pouvez supprimer la définition JSON d'une entité Azure Data Factory en cliquant sur le bouton **Ignorer** dans la barre d'outils.

## Clonage d’une entité Data Factory
Vous pouvez cloner une entité Azure Data Factory existante (service lié, table ou pipeline) en sélectionnant l'entité dans l'arborescence, puis en cliquant sur le **Cloner** dans la barre d'outils.

![Clonage d’une entité Data Factory][clone-datafactory-entity]

Vous verrez un nouveau brouillon créé sous le nœud **Brouillons** dans l'arborescence.

## Suppression d’une entité Data Factory
Vous pouvez supprimer une entité Azure Data Factory (service lié, table ou pipeline) en la sélectionnant dans l'arborescence, puis en cliquant sur **Supprimer** dans la barre d'outils, ou en cliquant avec le bouton droit sur l'entité, puis en cliquant sur **Supprimer**.

![Suppression d’une entité Data Factory][delete-datafactory-entity]

## Voir aussi
Consultez la rubrique [Prise en main du service Azure Data Factory][data-factory-get-started] pour obtenir des instructions étape par étape pour créer une fabrique de données Azure en utilisant Data Factory Editor.

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=July15_HO3-->