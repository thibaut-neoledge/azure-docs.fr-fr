<properties
	pageTitle="Concevez votre premier pipeline Azure Data Factory en utilisant Visual Studio"
	description="Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l'aide de Visual Studio."
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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Concevez votre premier pipeline Azure Data Factory en utilisant Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Dans cet article, vous apprendrez à utiliser Visual Studio pour créer votre premier pipeline. Ce didacticiel se déroule comme suit :

2.	Création de services liés (magasins de données, calculs).
3.	Création d'un groupe de données.
3.	Création d'un pipeline
4.	Créer une fabrique de données et déployer des services liés, un groupe de données et le pipeline. 

Cet article ne fournit pas une vue d'ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, consultez l'article [Présentation d’Azure Data Factory](data-factory-introduction.md).

> [AZURE.IMPORTANT]Passez en revue l'article [Vue d'ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et effectuez les étapes préalables avant de suivre ce didacticiel.

## Procédure pas à pas : créer et déployer des entités Data Factory à l’aide de Visual Studio 

### Conditions préalables

Vous devez avoir installé le logiciel suivant sur votre ordinateur : - Visual Studio 2013 - Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013. Accédez à la [page de téléchargement d'Azure](http://azure.microsoft.com/downloads/), puis cliquez sur **Installation de Visual Studio 2013** dans la section **.NET**. Mettez à jour « Visual Studio Tools For Azure Data Factory » avec la version la plus récente en sélectionnant Visual Studio 2013 --> Outils --> Mises à jour --> Galerie Visual Studio, puis en cliquant sur « Mettre à jour » dans l'entrée du plug-in « Visual Studio Tools for Azure Data Factory ».


### Créer le projet Visual Studio 
1. Lancez **Visual Studio 2013**. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. La boîte de dialogue **Nouveau projet** doit s'afficher.  
2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez le modèle **DataFactory**, puis cliquez sur **Projet Data Factory vide**. Si le modèle DataFactory n’est pas affiché, fermez Visual Studio, installez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, puis rouvrez Visual Studio.  

	![Boîte de dialogue Nouveau projet](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Entrez le **nom** du projet, son **emplacement** et le nom de la **solution**, puis cliquez sur **OK**.

	![Explorateur de solutions](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### créer des services liés
Dans cette étape, vous lierez votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Vous créerez également un jeu de données pour représenter les données de sortie issues d’un traitement Hive.


#### Créer le service lié Azure Storage


4. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.      
5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Service lié Azure Storage** dans la liste, puis cliquez sur **Ajouter**. 

	![Nouveau service lié](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Remplacez **accountname** et **accountkey** par le nom de votre compte de stockage Azure et par sa clé.

	![Service lié Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Enregistrez le fichier **AzureStorageLinkedService1.json**.

#### Créer le service lié Azure HDInsight
Vous allez maintenant créer un service lié pour le cluster HDInsight à la demande qui sera utilisé pour exécuter le script Hive.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter** puis cliquez sur **Nouvel élément**.
2. Sélectionnez **Service lié à la demande HDInsight** puis cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** par ce qui suit :

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
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

4. Enregistrez le fichier **HDInsightOnDemandLinkedService1.json**.
 
### Créer le jeu de données de sortie
Vous allez maintenant créer le jeu de données de sortie pour représenter les données stockées dans le stockage Azure Blob.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Azure Blob**dans la liste, puis cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** dans l'éditeur de la manière suivante : dans l'extrait de code JSON, vous créez un jeu de données appelé **AzureBlobOutput** et vous spécifiez la structure de données qui sera générée par le script Hive. En outre, vous spécifiez que les résultats sont stockés dans le conteneur d'objets blob appelé **données** et dans le dossier appelé **partitioneddata**. La section **disponibilité** spécifie que le jeu de données de sortie est généré sur une base mensuelle.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Enregistrez le fichier **AzureBlobLocation1.json**.


### Création de votre premier pipeline
Dans cette étape, vous allez créer votre premier pipeline.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Pipelines**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Pipeline de transformation Hive** dans la liste, puis cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** par l'extrait suivant :

	> [AZURE.IMPORTANT]remplacez **storageaccountname** par le nom de votre compte de stockage.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
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
	
	Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le scriptLinkedService, appelé **AzureStorageLinkedService1**) et dans un conteneur appelé **script**.

	La section **extendedProperties** permet de spécifier les paramètres d'exécution qui seront transmis au script Hive en tant que valeurs de configuration Hive (par ex. ${hiveconf:PartitionedData}).

	Les propriétés **start** et **end** du pipeline spécifient la période active du pipeline.

	Dans l'activité JSON, vous spécifiez que le script Hive s'exécute sur le calcul spécifié par le service lié **HDInsightOnDemandLinkedService**.
3. Enregistrez le fichier **HiveActivity1.json**.

### Ajouter partitionweblogs.hql en tant que dépendance 

1. Cliquez avec le bouton droit sur **Dépendences** Dans la fenêtre **Explorateur de solutions**, pointez sur **Ajouter**, puis cliquez sur **Élément existant**.  
2. Accédez au dossier **C:\\ADFGettingStarted**, sélectionnez le fichier **partitionweblogs.hql**, puis cliquez sur **Ajouter**. 

Lorsque vous publierez la solution à l’étape suivante, le fichier HQL sera chargé vers le conteneur de scripts de votre stockage d’objets blob.

### Publier/Déployer des entités Data Factory

18. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sur **Publier**. 
19. Si la boîte de dialogue **Connectez-vous à votre compte Microsoft** s'affiche, entrez vos informations d'identification pour le compte qui dispose de l'abonnement Azure, puis cliquez sur **Se connecter**.
20. La boîte de dialogue suivante doit s'afficher :

	![Boîte de dialogue Publier](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Dans la page Configurer une fabrique de données, procédez comme suit :
	1. Sélectionnez l'option **Créer une fabrique de données**.
	2. Entrez **FirstPipelineUsingVS** comme **Nom**.
	3. Sélectionnez l'abonnement correct dans le champ **Abonnement**. 
	4. Sélectionnez le **groupe de ressources** pour la fabrique de données à créer. 
	5. Sélectionnez la **région** pour la fabrique de données. 
	6. Cliquez sur **Suivant** pour basculer vers la page **Publier des éléments**. (utilisez la touche **TABULATION** pour passer au champ Nom si le bouton **Suivant** est désactivé.) 
23. Dans la page **Publier des éléments**, vérifiez que toutes les entités de fabriques de données sont sélectionnées, puis cliquez sur **Suivant** pour basculer vers la page **Résumé**.     
24. Passez en revue le résumé, puis cliquez sur **Suivant** pour démarrer le processus de déploiement et afficher l'**état du déploiement**.
25. Dans la page **État du déploiement**, vous devez voir l'état du processus de déploiement. Une fois le déploiement terminé, cliquez sur Terminer. 
 

## Utiliser l’Explorateur de serveurs pour passer en revue les entités Data Factory

1. Dans **Visual Studio**, cliquez sur **Affichage** dans le menu, puis sur **Explorateur de serveurs**.
2. Dans la fenêtre Explorateur de serveurs, développez **Azure** et **Data Factory**. Si la boîte de dialogue **Se connecter à Visual Studio** est affichée, entrez le **compte** associé à votre abonnement Azure, puis cliquez sur **Continuer**. Entrez le **mot de passe**, puis cliquez sur **Se connecter**. Visual Studio essaie d’obtenir des informations sur toutes les fabriques de données Azure contenues dans votre abonnement. L'état de cette opération s'affiche dans la fenêtre **Liste des tâches de Data Factory**.

	![Explorateur de serveurs](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Vous pouvez cliquer avec le bouton droit sur une fabrique de données et sélectionner **Exporter la fabrique de données vers le nouveau projet** pour créer un projet Visual Studio basé sur une fabrique de données existante.

	![Exporter la fabrique de données](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Mettre à jour des outils Data Factory pour Visual Studio

Pour mettre à jour des outils Azure Data Factory pour Visual Studio, procédez comme suit :

1. Dans le menu, cliquez sur **Outils**, puis sélectionnez **Extensions et mises à jour**.
2. Dans le volet gauche, sélectionnez **Mises à jour**, puis **Galerie Visual Studio**.
3. Sélectionnez **Outils Azure Data Factory pour Visual Studio**, puis cliquez sur **Mettre à jour**. Si cette entrée n’est pas affichée, c’est que vous possédez déjà la dernière version de ces outils. 

Consultez [Surveiller les jeux de données et le pipeline](data-factory-monitor-manage-pipelines.md) pour obtenir des instructions sur l'utilisation du portail Azure en version préliminaire afin de surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.
 

## Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster HDInsight à la demande. Pour apprendre à utiliser une activité de copie pour copier des données à partir d'un objet blob Azure dans Azure SQL, consultez le [Didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](data-factory-get-started.md).
  
## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=Oct15_HO2-->