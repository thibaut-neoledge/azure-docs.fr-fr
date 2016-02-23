<properties
	pageTitle="Prise en main d’Azure Data Factory (Visual Studio)"
	description="Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory avec Visual Studio."
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
	ms.date="02/16/2015"
	ms.author="spelluru"/>

# Prise en main d’Azure Data Factory (Visual Studio)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Dans cet article, vous allez découvrir comment utiliser Microsoft Visual Studio pour créer votre première fabrique de données Azure.

## Conditions préalables

1. Vous **devez** lire l’article [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et effectuer les étapes préalables avant de continuer.
2. Cet article ne fournit pas de vue d’ensemble conceptuelle du service Azure Data Factory. Nous vous recommandons de lire l’article [Introduction à Azure Data Factory](data-factory-introduction.md) pour une présentation détaillée du service.  

## Procédure pas à pas : créer et déployer des entités Data Factory à l’aide de Visual Studio 

### Conditions préalables

Les composants suivants doivent être installés sur votre ordinateur :

- Visual Studio 2013 ou Visual Studio 2015
- Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013 ou Visual Studio 2015. Accédez à la [page de téléchargement d’Azure](https://azure.microsoft.com/downloads/), puis cliquez sur **VS 2013** ou **VS 2015** dans la section **.NET**.
- Téléchargez le dernier plug-in Azure Data Factory pour Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si vous utilisez Visual Studio 2013, vous pouvez également mettre à jour le plug-in de la manière suivante : dans le menu, cliquez sur **Outils** -> **Extensions et mises à jour** -> **En ligne** -> **Galerie Visual Studio** -> **Outils Microsoft Azure Data Factory pour Visual Studio** -> **Mettre à jour**. 
	
	

### Créer le projet Visual Studio 
1. Lancez **Visual Studio 2013** ou **Visual Studio 2015**. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. La boîte de dialogue **Nouveau projet** doit s’afficher.  
2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez le modèle **DataFactory**, puis cliquez sur **Projet Data Factory vide**.   

	![Boîte de dialogue Nouveau projet](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Entrez le **nom** du projet, son **emplacement** et le nom de la **solution**, puis cliquez sur **OK**.

	![Explorateur de solutions](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Créer des services liés
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline peut contenir une ou plusieurs activités. Par exemple, une activité de copie pour copier des données d’une source vers un magasin de données de destination, et une activité Hive HDInsight pour exécuter un script Hive pour transformer des données d’entrée et produire des données de sortie. Vous spécifiez le nom et les paramètres de la fabrique de données ultérieurement, quand vous publiez votre solution Data Factory.

Dans cette étape, vous allez lier votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Le compte de stockage Azure contient les données d’entrée et de sortie pour le pipeline de cet exemple. Le service lié HDInsight est utilisé pour exécuter le script Hive spécifié dans l’activité du pipeline de cet exemple. Vous devez identifier les services de magasin de données/de calcul qui sont utilisés dans votre scénario et les lier à la fabrique de données en créant des services liés.

#### Créer le service lié Azure Storage
Dans cette étape, vous allez lier votre compte de stockage Azure à votre fabrique de données. Pour les besoins de ce didacticiel, vous utilisez le même compte de stockage Azure pour stocker les données d’entrée/sortie et le fichier de script HQL.

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.      
5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Service lié Azure Storage** dans la liste, puis cliquez sur **Ajouter**. 
3. Remplacez **accountname** et **accountkey** par le nom de votre compte de stockage Azure et par sa clé. Pour découvrir comment obtenir votre clé d’accès de stockage, consultez [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	![Service lié Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Enregistrez le fichier **AzureStorageLinkedService1.json**.

#### Créer le service lié Azure HDInsight
Dans cette étape, vous allez lier un cluster HDInsight à la demande à votre fabrique de données. Le cluster HDInsight est automatiquement créé lors de l’exécution, puis supprimé une fois le traitement effectué et au terme du délai d’inactivité spécifié. Vous pouvez utiliser votre propre cluster HDInsight au lieu d’utiliser un cluster HDInsight à la demande. Pour plus d’informations, consultez [Services de calcul liés](data-factory-compute-linked-services.md).

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Services liés**, pointez sur **Ajouter** puis cliquez sur **Nouvel élément**.
2. Sélectionnez **Service lié à la demande HDInsight** puis cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** par ce qui suit :

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
		  }
		}
	
	Le tableau suivant décrit les propriétés JSON utilisées dans l'extrait de code :
	
	Propriété | Description
	-------- | -----------
	Version | Cette propriété indique que la version de service HDInsight doit être la version 3.2. 
	ClusterSize | Cette propriété crée un cluster HDInsight avec un seul nœud. 
	TimeToLive | Cette propriété spécifie la durée d'inactivité du cluster HDInsight, avant sa suppression.
	linkedServiceName | Cette propriété spécifie le compte de stockage qui sera utilisé pour stocker les journaux générés par HDInsight.

	Notez les points suivants :
	
	- La fabrique de données crée pour vous un cluster HDInsight **Windows** avec le JSON ci-dessus. Vous pouvez également lui faire créer un cluster HDInsight **Linux**. Consultez [Service lié HDInsight à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) pour plus d’informations. 
	- Vous pouvez utiliser **votre propre cluster HDInsight** au lieu d’utiliser un cluster HDInsight à la demande. Consultez [Service lié HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) pour plus d’informations.
	- Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. C’est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il existe un cluster activé (**timeToLive**) et est supprimé une fois le traitement activé.
	
		Comme un nombre croissant de tranches sont traitées, vous verrez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs suit un modèle : « adf**yourdatafactoryname**-**linkedservicename**- datetimestamp ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.

	Consultez [Service lié HDInsight à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) pour plus d’informations. 
4. Enregistrez le fichier **HDInsightOnDemandLinkedService1.json**.

### Créer des jeux de données
Dans cette étape, vous allez créer des jeux de données pour représenter les données d’entrée et de sortie pour le traitement Hive. Ces jeux de données font référence au service **AzureStorageLinkedService1** que vous avez créé précédemment dans ce didacticiel. Le service lié pointe vers un compte de stockage Azure, et les jeux de données spécifient le conteneur, le dossier et le nom de fichier dans le stockage qui contient les données d’entrée et de sortie.

#### Créer la table d’entrée

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Objet blob Azure** dans la liste, changez le nom du fichier en **OutputDataset.json**, puis cliquez sur **Ajouter**.
3. Remplacez le **code JSON** dans l’éditeur par ce qui suit : 

	Dans l’extrait de code JSON, vous créez un jeu de données appelé **AzureBlobInput**, qui représente les données d’entrée pour une activité dans le pipeline. En outre, vous spécifiez que les données d’entrée se trouvent dans le conteneur d’objets blob nommé **adfgetstarted** et dans le dossier nommé **inputdata**.
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService1",
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

	Le tableau suivant décrit les propriétés JSON utilisées dans l'extrait de code :

	| Propriété | Description |
	| :------- | :---------- |
	| type | La propriété type est définie sur AzureBlob, car les données se trouvent dans le stockage d’objets blob Azure. |  
	| linkedServiceName | fait référence au service AzureStorageLinkedService1 que vous avez créé précédemment. |
	| fileName | Cette propriété est facultative. Si vous omettez cette propriété, tous les fichiers spécifiés dans le paramètre folderPath sont récupérés. Dans le cas présent, seul le fichier input.log est traité. |
	| type | Les fichiers journaux sont au format texte : nous allons donc utiliser TextFormat. | 
	| columnDelimiter | Les colonnes des fichiers journaux sont délimitées par « , » (virgule) |
	| frequency/interval | La fréquence est définie sur Mois et l’intervalle est 1, ce qui signifie que les segments d’entrée sont disponibles mensuellement. | 
	| external | Cette propriété a la valeur true si les données d’entrée ne sont pas générées par le service Data Factory. | 
	  
	
3. Enregistrez le fichier **InputDataset.json**.

 
#### Créer le jeu de données de sortie
Vous allez maintenant créer le jeu de données de sortie pour représenter les données de sortie stockées dans le stockage d’objets blob Azure.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Tables**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Objet blob Azure** dans la liste, changez le nom du fichier en **OutputDataset.json**, puis cliquez sur **Ajouter**. 
3. Remplacez le **code JSON** dans l’éditeur par ce qui suit : 

	Dans l’extrait de code JSON, créez un jeu de données appelé **AzureBlobOutput** et spécifiez la structure des données qui seront produites par le script Hive. Indiquez aussi que les résultats sont stockés dans le conteneur d’objets blob appelé **adfgetstarted** et dans le dossier appelé **partitioneddata**. La section **availability** spécifie que le jeu de données de sortie est produit tous les mois.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Enregistrez le fichier **OutputDataset.json**.


### Création de votre premier pipeline
Dans cette étape, vous allez créer votre premier pipeline avec une activité **HDInsightHive**. Notez que le segment d’entrée est disponible mensuellement (fréquence : Mois, intervalle : 1), que le segment de sortie est produit mensuellement et que la propriété du planificateur pour l’activité est également définie sur Mensuellement (voir ci-dessous). Les paramètres pour le jeu de données de sortie et le planificateur d’activité doivent correspondre. À ce stade, le jeu de données de sortie est ce qui pilote la planification : vous devez donc créer un jeu de données de sortie même si l’activité ne génère aucune sortie. Si l’activité ne prend aucune entrée, vous pouvez ignorer la création du jeu de données d’entrée. Les propriétés utilisées dans le code JSON suivant sont expliquées à la fin de cette section.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Pipelines**, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**. 
2. Sélectionnez **Pipeline de transformation Hive** dans la liste, puis cliquez sur **Ajouter**. 
3. Remplacez le code **JSON** par l'extrait suivant :

	> [AZURE.IMPORTANT] remplacez **storageaccountname** par le nom de votre compte de stockage.

		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService1",
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

 	Dans l’extrait de code JSON, vous créez un pipeline qui se compose d’une seule activité utilisant Hive pour traiter des données sur un cluster HDInsight.
	
	Dans l’extrait de code JSON, vous créez un pipeline qui se compose d’une seule activité utilisant Hive pour traiter des données sur un cluster HDInsight.
	
	Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le service scriptLinkedService, appelé **AzureStorageLinkedService1**) et dans le dossier **script** du conteneur **adfgetstarted**.

	La section **defines** est utilisée pour spécifier les paramètres d’exécution qui seront passés au script Hive comme valeurs de configuration Hive (par exemple ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Les propriétés **start** et **end** du pipeline spécifient la période active du pipeline.

	Dans l’activité JSON, vous spécifiez que le script Hive s’exécute sur le calcul spécifié par le service **linkedServiceName** – **HDInsightOnDemandLinkedService**.

3. Enregistrez le fichier **HiveActivity1.json**.

### Ajouter partitionweblogs.hql et input.log comme dépendance 

1. Cliquez avec le bouton droit sur **Dépendances** dans la fenêtre **Explorateur de solutions**, pointez sur **Ajouter**, puis cliquez sur **Élément existant**.  
2. Accédez au dossier **C:\ADFGettingStarted**, sélectionnez les fichiers **partitionweblogs.hql** et **input.log**, puis cliquez sur **Ajouter**. Vous avez créé ces deux fichiers dans le cadre de la configuration requise dans la [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md).

Quand vous publiez la solution à l’étape suivante, le fichier **partitionweblogs.hql** est chargé dans le dossier scripts du conteneur d’objets blob **adfgetstarted**.

### Publier/déployer des entités Data Factory

18. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**. 
19. Si la boîte de dialogue **Connectez-vous à votre compte Microsoft** s'affiche, entrez vos informations d'identification pour le compte qui dispose de l'abonnement Azure, puis cliquez sur **Se connecter**.
20. La boîte de dialogue suivante doit s’afficher :

	![Boîte de dialogue Publier](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Dans la page Configurer une fabrique de données, procédez comme suit :
	1. Sélectionnez l’option **Créer une fabrique de données**.
	2. Entrez **FirstDataFactoryUsingVS** pour le **Nom**. 
	
		> [AZURE.IMPORTANT] Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur **Le nom de la fabrique de données « FirstDataFactoryUsingVS » n’est pas disponible** au moment de la publication, changez le nom (par exemple en votrenomFirstDataFactoryUsingVS). Consultez la rubrique [Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
		> 
		> Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.
	3. Sélectionnez l’abonnement approprié pour le champ **Abonnement**. 
	4. Sélectionnez le **groupe de ressources** pour la fabrique de données à créer. 
	5. Sélectionnez la **région** pour la fabrique de données. 
	6. Cliquez sur **Suivant** pour basculer vers la page **Publier des éléments**. (utilisez la touche **TABULATION** pour passer au champ Nom si le bouton **Suivant** est désactivé.) 
23. Dans la page **Publier des éléments**, vérifiez que toutes les entités de fabriques de données sont sélectionnées, puis cliquez sur **Suivant** pour basculer vers la page **Résumé**.     
24. Passez en revue le résumé, puis cliquez sur **Suivant** pour démarrer le processus de déploiement et afficher l’**état du déploiement**.
25. Dans la page **État du déploiement**, vous devez voir l’état du processus de déploiement. Une fois le déploiement terminé, cliquez sur Terminer. 
 
## Étape 4 : Surveiller le pipeline

6. Connectez-vous au [portail Azure](https://portal.azure.com/) et procédez comme suit :
	1. Cliquez sur **Parcourir** et sélectionnez **Fabriques de données**. 
		![Parcourir les fabriques de données](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
	2. Sélectionnez **FirstDataFactoryUsingVS** dans la liste des fabriques de données. 
7. Dans la page d’accueil de votre fabrique de données, cliquez sur **Diagramme**.
  
	![Vignette de diagramme](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. Dans la vue de diagramme, une présentation des pipelines et des jeux de données utilisés dans ce didacticiel s’affiche.
	
	![Vue de diagramme](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Pour afficher toutes les activités du pipeline, cliquez avec le bouton droit sur le pipeline dans le diagramme, puis cliquez sur Ouvrir un pipeline. 

	![Menu Ouvrir un pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Vérifiez que l’activité HDInsightHive est bien dans le pipeline. 
  
	![Vue Ouvrir un pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

	Pour revenir à la vue précédente, cliquez sur **Fabrique de données** dans le menu de navigation du haut. 
10. Dans la **Vue de diagramme**, double-cliquez sur le jeu de données **AzureBlobInput**. Vérifiez que l’état du segment est **Prêt**. Plusieurs minutes peuvent être nécessaires avant que le segment n’apparaisse avec l’état Prêt. Si rien ne se produit au bout d’un moment, vérifiez que le fichier d’entrée (input.log) est placé dans le conteneur (adfgetstarted) et le dossier (inputdata) appropriés.

	![Segment d’entrée dans l’état Prêt](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Cliquez sur **X** pour fermer le panneau **AzureBlobInput**. 
12. Dans la **Vue de diagramme**, double-cliquez sur le jeu de données **AzureBlobOutput**. Le segment est en cours de traitement.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Quand le traitement est terminé, l’état du segment devient **Prêt**.
	>[AZURE.IMPORTANT] La création d’un cluster HDInsight à la demande prend généralement un certain temps (environ 20 minutes).  

	![Dataset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)
	
10. Quand l’état du segment est **Prêt**, vérifiez la présence des données de sortie dans le dossier **partitioneddata** du conteneur **adfgetstarted** de votre stockage d’objets blob.
 
	![données de sortie](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

Consultez [Surveiller les jeux de données et le pipeline](data-factory-monitor-manage-pipelines.md) pour obtenir des instructions sur l’utilisation du portail Azure pour surveiller le pipeline et les jeux de données que vous avez créés dans ce didacticiel.

## Utiliser l’Explorateur de serveurs pour passer en revue les entités Data Factory

1. Dans **Visual Studio**, cliquez sur **Affichage** dans le menu, puis sur **Explorateur de serveurs**.
2. Dans la fenêtre Explorateur de serveurs, développez **Azure**, puis **Data Factory**. Si la boîte de dialogue **Se connecter à Visual Studio** est affichée, entrez le **compte** associé à votre abonnement Azure, puis cliquez sur **Continuer**. Entrez le **mot de passe**, puis cliquez sur **Se connecter**. Visual Studio essaie d’obtenir des informations sur toutes les fabriques de données Azure contenues dans votre abonnement. L’état de cette opération s’affiche dans la fenêtre **Liste des tâches de Data Factory**.

	![Explorateur de serveurs](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Vous pouvez cliquer avec le bouton droit sur une fabrique de données et sélectionner **Exporter la fabrique de données vers le nouveau projet** pour créer un projet Visual Studio basé sur une fabrique de données existante.

	![Exporter la fabrique de données](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Mettre à jour des outils Data Factory pour Visual Studio

Pour mettre à jour des outils Azure Data Factory pour Visual Studio, procédez comme suit :

1. Dans le menu, cliquez sur **Outils**, puis sélectionnez **Extensions et mises à jour**.
2. Dans le volet gauche, sélectionnez **Mises à jour**, puis **Galerie Visual Studio**.
3. Sélectionnez **Outils Azure Data Factory pour Visual Studio**, puis cliquez sur **Mettre à jour**. Si cette entrée n’est pas affichée, c’est que vous possédez déjà la dernière version de ces outils. 

## Utiliser des fichiers de configuration
Vous pouvez utiliser des fichiers de configuration dans Visual Studio pour configurer les propriétés des services/tableaux/pipelines liés différemment pour chaque environnement.

Examinez la définition JSON suivante pour un service lié Azure Storage. Spécifiez **connectionString** avec différentes valeurs pour accountname et accountkey en fonction de l’environnement (Dev/Test/Production) sur lequel vous déployez des entités Data Factory. Vous pouvez le faire à l’aide d’un fichier de configuration distinct pour chaque environnement.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	} 

### Ajouter un fichier de configuration
Ajoutez un fichier de configuration pour chaque environnement en effectuant les opérations suivantes :

1. Cliquez sur le projet Data Factory dans votre solution Visual Studio, pointez sur **Ajouter**, puis cliquez sur **Nouvel élément**.
2. Sélectionnez **Config** dans la liste des modèles installés sur la gauche, sélectionnez **Fichier de Configuration**, entrez un **nom** pour le fichier de configuration, puis cliquez sur **Ajouter**.

	![Ajouter un fichier de configuration](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Ajouter des paramètres de configuration et leurs valeurs selon le format indiqué ci-dessous :

		{
		    "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
		    "AzureStorageLinkedService1": [
		        {
		            "name": "$.properties.typeProperties.connectionString",
		            "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    ],
		    "AzureSqlLinkedService1": [
		        {
		            "name": "$.properties.typeProperties.connectionString",
		            "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		        }
		    ]
		}

	Cet exemple configure la propriété connectionString d’un service lié Azure Storage et d’un service lié SQL Azure. Notez que la syntaxe de spécification du nom est [JsonPath](http://goessner.net/articles/JsonPath/).

	Si JSON est doté d’une propriété ayant un tableau de valeurs comme indiqué ci-dessous :

		"structure": [
	  		{
	  			"name": "FirstName",
	    		"type": "String"
	  		},
	  		{
	    		"name": "LastName",
	    	    "type": "String"
			}
		],
	
	Vous devez appliquer la configuration suivante dans le fichier de configuration (utilisez une indexation de base zéro) :
		
		{
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }


### Déployer une solution à l’aide d’une configuration
Lorsque vous publiez des entités Azure Data Factory dans Visual Studio, vous pouvez spécifier la configuration que vous souhaitez utiliser pour cette opération de publication.

Pour publier des entités dans un projet Azure Data Factory à l’aide d’un fichier de configuration :

1. Cliquez avec le bouton droit sur le projet Data Factory et cliquez sur **Publier** pour voir la boîte de dialogue **Publier des éléments**. 
2. Sur la page **Configurer une fabrique de données**, sélectionnez une fabrique de données existante ou spécifiez des valeurs pour créer une fabrique de données, puis cliquez sur **Suivant**.   
3. Sur la page **Publier des éléments** : vous verrez une liste déroulante avec les configurations disponibles pour le champ **Sélectionner une configuration de déploiement**.

	![Sélectionner un fichier de config](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Sélectionnez le **fichier de configuration** que vous souhaitez utiliser et cliquez sur **Suivant**.
5. Assurez-vous de voir le nom du fichier JSON à la page **Résumé** et cliquez sur **Suivant**. 
6. Une fois l’opération de déploiement terminée, cliquez sur **Terminer**. 

Au cours du déploiement, les valeurs du fichier de configuration sont utilisées pour définir celles des propriétés des fichiers JSON pour les entités Data Factory (services, tableaux ou pipelines liés) avant que les entités ne soient déployées sur le service Azure Data Factory.

## Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster HDInsight à la demande. Pour voir comment utiliser une activité de copie pour copier des données depuis un objet blob Azure vers Azure SQL, consultez le [Didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](data-factory-get-started.md).
  

<!---HONumber=AcomDC_0218_2016-->