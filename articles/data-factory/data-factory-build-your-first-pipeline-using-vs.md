<properties
	pageTitle="Concevez votre premier pipeline en utilisant Azure Data Factory"
	description="Ce didacticiel vous montre comment créer un pipeline de données d’exemple qui transforme des données à l'aide d'Azure HDInsight, en utilisant Visual Studio"
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
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>


# Concevez votre premier pipeline en utilisant Azure Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Dans cet article, vous apprendrez à utiliser Visual Studio pour créer votre premier pipeline. Ce didacticiel se déroule comme suit :

1.	Création de la fabrique de données
2.	Création des services liés (magasins de données, calculs) et des jeux de données
3.	Création du pipeline

Cet article ne fournit pas une vue d'ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, consultez l'article [Présentation d’Azure Data Factory](data-factory-introduction.md).

## Étape 1 : création de la fabrique de données

1.	Après la connexion au [portail Azure en version préliminaire](http://portal.azure.com/), procédez comme suit :
	1.	Cliquez sur **NOUVEAU** dans le coin inférieur gauche. 
	2.	Cliquez sur **Analyse des données** dans le panneau **Créer**.
	3.	Cliquez sur **Data Factory** dans le panneau **Analyse des données**.

		![Panneau Créer](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	Dans le panneau **Nouvelle fabrique de données**, saisissez **DataFactoryMyFirstPipeline** en guise de nom.

	![Panneau Nouvelle fabrique de données](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] Les noms Azure Data Factory sont globalement uniques. Vous devez faire précéder le nom de la fabrique de données par votre nom, pour activer la création de la fabrique. 
3.	Si vous n’avez pas créé de groupe de ressources, vous devez en créer un. Pour ce faire :
	1.	Cliquez sur **NOM DU GROUPE DE RESSOURCES**.
	2.	Sélectionnez **Créer un groupe de ressources** dans le panneau **Groupe de ressources**.
	3.	Saisissez **ADF** pour le **Nom** dans le panneau **Créer un groupe de ressources**.
	4.	Cliquez sur **OK**.
	
		![Créer un groupe de ressources](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	Après avoir sélectionné le groupe de ressources, vérifiez que vous utilisez l’abonnement correspondant à celui dans lequel vous souhaitez créer la fabrique de données.
5.	Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.
6.	La fabrique de données apparaît comme étant en cours de création dans le **Tableau d'accueil** du portail Azure en version préliminaire, comme suit :   

	![État de la création de la fabrique de données](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. Félicitations ! Vous avez créé votre première fabrique de données. Une fois la fabrique de données créée, vous verrez la page correspondante indiquant son contenu. 	

	![Panneau Data Factory](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

Dans les étapes suivantes, vous apprendrez à créer les services liés, les jeux de données et le pipeline que vous utiliserez dans ce didacticiel.

## Procédure pas à pas : créer et déployer des entités Data Factory à l’aide de Visual Studio 

### Conditions préalables

Vous devez avoir installé le logiciel suivant sur votre ordinateur :
- Visual Studio 2013
- Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013. Accédez à la [page de téléchargement d’Azure](http://azure.microsoft.com/downloads/), puis cliquez sur **Installation de Visual Studio 2013** dans la section **.NET**.


### Créer le projet Visual Studio 
1. Lancez **Visual Studio 2013**. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. La boîte de dialogue **Nouveau projet** doit s’afficher.  
2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez le modèle **DataFactory**, puis cliquez sur **Projet Data Factory vide**. Si le modèle DataFactory n’est pas affiché, fermez Visual Studio, installez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, puis rouvrez Visual Studio.  

	![Boîte de dialogue Nouveau projet](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Entrez le **nom** du projet, son **emplacement** et le nom de la **solution**, puis cliquez sur **OK**.

	![Explorateur de solutions](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### créer des services liés
Dans cette étape, vous lierez votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Vous créerez également un jeu de données pour représenter les données de sortie issues d’un traitement Hive.


#### Créer le service lié Azure Storage


4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.      
5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Service lié Azure Storage** dans la liste, puis cliquez sur **Ajouter**. 

	![Nouveau service lié](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Remplacez **accountname** et **accountkey** par le nom de votre compte de stockage Azure et par sa clé.

	![Service lié Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Enregistrez le fichier **AzureStorageLinkedService1.json**.

#### Créer le service lié Azure HDInsight
Vous allez maintenant créer un service lié pour le cluster HDInsight à la demande qui sera utilisé pour exécuter le script Hive.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter** puis cliquez sur **Nouvel élément**.
2. Sélectionnez **Service lié à la demande HDInsight** puis cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** par ce qui suit :

		{
		    "name": "HDInsightOnDemandLinkedService",
		    "properties": {
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
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

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Azure Blob**dans la liste et cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** dans l'éditeur comme suit : dans l’extrait de code JSON, vous créez un jeu de données appelé **AzureBlobOutput** et vous spécifiez la structure de données qui sera générée par le script Hive. En outre, vous spécifiez que les résultats sont stockés dans le conteneur d'objets blob appelé **données** et dans le dossier appelé **partitioneddata**. La section **disponibilité** spécifie que le jeu de données de sortie est généré sur une base mensuelle.
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
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

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Pipelines**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Pipeline de transformation Hive** dans la liste et cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** par l'extrait de code suivant et remplacez **storageaccountname** par le nom de votre compte de stockage.

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
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
3. Enregistrez le fichier **HiveActivity1.json**. 

### Publier/Déployer des entités Data Factory
  
1. Dans la zone des barres d’outils, cliquez avec le bouton droit sur **Data Factory** et sélectionnez cet élément pour activer la barre d’outils Data Factory, si ce n’est déjà fait. 
19. Dans la **barre d’outils Data Factory**, cliquez sur la **zone de liste déroulante** pour voir toutes les fabriques de données de votre abonnement Azure. Si la boîte de dialogue **Se connecter à Visual Studio** s’affiche : 
	20. Entrez le **compte de messagerie** associé à l’abonnement Azure dans lequel vous souhaitez créer la fabrique de données, entrez le **mot de passe**, puis cliquez sur **Connexion**.
	21. Une fois la connexion établie, vous devez voir toutes les fabriques de données dans l’abonnement Azure. Dans ce didacticiel, vous allez créer une fabrique de données.       
22. Dans la liste déroulante, sélectionnez **DataFactoryMyFirstPipeline**, puis cliquez sur le bouton **Publier** pour déployer/publier les services liés, les jeux de données et le pipeline.    

	![Bouton Publier](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. L’état de la publication doit s’afficher dans la fenêtre **Liste des tâches de Data Factory** qui est illustrée dans l’image ci-dessus. Vérifiez que la publication a abouti.


## Utiliser l’Explorateur de serveurs pour passer en revue les entités Data Factory

1. Dans **Visual Studio**, cliquez sur **Affichage** dans le menu, puis sur **Explorateur de serveurs**.
2. Dans la fenêtre Explorateur de serveurs, développez **Azure** et **Data Factory**. Si la boîte de dialogue **Se connecter à Visual Studio** est affichée, entrez le **compte** associé à votre abonnement Azure, puis cliquez sur **Continuer**. Entrez le **mot de passe**, puis cliquez sur **Se connecter**. Visual Studio essaie d’obtenir des informations sur toutes les fabriques de données Azure contenues dans votre abonnement. L’état de cette opération s’affiche dans la fenêtre **Liste des tâches de Data Factory**.

	![Explorateur de serveurs](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Vous pouvez cliquer avec le bouton droit sur une fabrique de données et sélectionner **Exporter la fabrique de données vers le nouveau projet** pour créer un projet Visual Studio basé sur une fabrique de données existante.

	![Exporter la fabrique de données](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Mettre à jour des outils Data Factory pour Visual Studio

Pour mettre à jour des outils Azure Data Factory pour Visual Studio, procédez comme suit :

1. Dans le menu, cliquez sur **Outils**, puis sélectionnez **Extensions et mises à jour**.
2. Dans le volet gauche, sélectionnez **Mises à jour**, puis **Galerie Visual Studio**.
3. Sélectionnez **Outils Azure Data Factory pour Visual Studio** et cliquez sur **Mettre à jour**. Si cette entrée n’est pas affichée, c’est que vous possédez déjà la dernière version de ces outils. 

Consultez [Surveiller les jeux de données et le pipeline](data-factory-monitor-manage-pipelines.md) pour obtenir des instructions sur l’utilisation du portail Azure en version préliminaire afin de surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.
 

## Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster HDInsight à la demande. Pour apprendre à utiliser une activité de copie pour copier des données à partir d'un objet blob Azure dans Azure SQL, consultez le [didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](data-factory-get-started.md).
  

<!------HONumber=August15_HO6-->