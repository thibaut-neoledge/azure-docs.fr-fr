<properties 
	pageTitle="Prise en main d'Azure Data Factory" 
	description="Ce didacticiel vous montre comment créer un exemple de pipeline de données qui copie les données d'un objet blob vers une instance de base de données SQL Azure." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Didacticiel : Créer et surveiller une fabrique de données à l'aide de données fabrique éditeur
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##Dans ce didacticiel
Ce didacticiel contient les étapes suivantes :

Étape | Description
-----| -----------
[Étape 1: Créer une fabrique de données Azure](#CreateDataFactory) | Dans cette étape, vous allez créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.  
[Étape 2: Créer des services liés](#CreateLinkedServices) | Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. Le StorageLinkedService lie le stockage Azure et AzureSqlLinkedService lie la base de données SQL Azure à l'ADFTutorialDataFactory. Les données d'entrée pour le pipeline se trouvent dans un conteneur d'objets blob dans le stockage blob Azure et les données de sortie seront stockées dans une table dans la base de données SQL Azure. Par conséquent, vous ajoutez ces deux banques de données en tant que services liés à la fabrication de données.      
[Étape 3: Créer d'entrée et sortie de tables](#CreateInputAndOutputDataSets) | Dans l'étape précédente, vous avez créé des services liés qui font référence à des magasins de données qui contiennent des données d'entrée/sortie. Dans cette étape, vous allez définir deux tables de fabrique de données-- **EmpTableFromBlob** et **EmpSQLTable** --qui représentent les données d'entrée/sortie sont stockées dans les banques de données. Pour le EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données et pour le EmpSQLTable, vous spécifiez la table SQL qui stocke les données de sortie. Vous devez également spécifier d'autres propriétés telles que la structure des données, la disponibilité des données, etc.... 
[Étape 4: Créer et exécuter un pipeline](#CreateAndRunAPipeline) | Dans cette étape, vous allez créer un pipeline nommé **ADFTutorialPipeline** dans le ADFTutorialDataFactory. Le pipeline aura une **activité copie** que copie d'entrée des données Azure blob dans la table SQL Azure de sortie.
[Étape 5: Surveiller les secteurs et pipeline](#MonitorDataSetsAndPipeline) | Dans cette étape, vous allez surveiller les tranches de tables d'entrée et de sortie à l'aide du portail Azure Preview.
 

## <a name="CreateDataFactory"></a>Étape 1: Créer une fabrique de données Azure
Dans cette étape, vous utilisez le portail Azure Preview pour créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.

1.	Après la connexion à la [portail Azure Preview][azure-preview-portal], cliquez sur **nouveau** dans le coin inférieur gauche, sélectionnez **l'analyse de données** dans les **créer** lame, puis cliquez sur **Data Factory** dans le **l'analyse de données** lame. 

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]

6. Dans le **nouvelle fabrique de données** lame :
	1. Entrez **ADFTutorialDataFactory** pour le **nom**. 
	
  		![Nouvelle lame fabrique][image-data-factory-getstarted-new-data-factory-blade]
	2. Cliquez sur **nom de groupe de ressources** et procédez comme suit :
		1. Cliquez sur **créer un groupe de ressources**.
		2. Dans le **créer un groupe de ressources** lame, entrez **ADFTutorialResourceGroup** pour le **nom** du groupe de ressources, cliquez sur **OK**. 

			![Créer le groupe de ressources][image-data-factory-create-resource-group]

		Certaines étapes de ce didacticiel supposent que vous utilisez le nom : **ADFTutorialResourceGroup** pour le groupe de ressources. Pour en savoir plus sur les groupes de ressources, consultez [à l'aide de groupes de ressources pour gérer vos ressources Azure](resource-group-overview.md).  
7. Dans le **nouvelle fabrique de données** lame, notez que **Ajouter au tableau d'accueil** est sélectionnée.
8. Cliquez sur **créer** dans les **nouvelle fabrique de données** lame.

	> [AZURE.NOTE]Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez une erreur : **nom d'usine de données « ADFTutorialDataFactory » n'est pas disponible**, modifiez le nom de la fabrique de données (par exemple, yournameADFTutorialDataFactory) et essayez à nouveau de créer. Utilisez ce nom ADFTutorialFactory lors de l'exécution des étapes restantes dans ce didacticiel. Voir [Factory de données - règles d'appellation][data-factory-naming-rules] rubrique pour les règles d'affectation de noms pour les artefacts de la fabrique de données.
	 
	![Nom de fabrique de données non disponible][image-data-factory-name-not-available]

9. Cliquez sur **NOTIFICATIONS** concentrateur à gauche et recherchez des notifications à partir du processus de création. Cliquez sur **X** pour fermer la **NOTIFICATIONS** lame si elle est ouverte.
10. Une fois la création terminée, vous verrez la **DATA FACTORY** lame comme indiqué ci-dessous.

    ![Page d'accueil de fabrique de données][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Étape 2: Créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un emplacement Azure Storage, une base de données SQL Azure ou une base de données SQL Server locale.

Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. StorageLinkedService liées à des liaisons de service un compte Azure Storage et AzureSqlLinkedService lie une base de données SQL Azure pour le **ADFTutorialDataFactory**. Vous allez créer un pipeline plus loin dans ce didacticiel qui copie les données à partir d'un conteneur d'objets blob StorageLinkedService dans une table SQL AzureSqlLinkedService.

### Créer un service lié pour le compte de stockage Azure
1.	Dans le **DATA FACTORY** lame, cliquez sur **auteur et déployer** vignette pour lancer le **éditeur** pour la fabrique de données.

	![Vignette Créer et déployer][image-author-deploy-tile]

	> [AZURE.NOTE]Voir [données fabrique éditeur][data-factory-editor] rubrique pour une présentation détaillée de l'éditeur de la fabrique de données.
	 
5. Dans le **éditeur**, cliquez sur **nouveau magasin de données** bouton sur la barre d'outils et sélectionnez **stockage Azure** dans le menu déroulant. Vous devez voir le modèle JSON pour la création d'un service de stockage Azure lié dans le volet droit.

	![Éditeur nouveau bouton de magasin de données][image-editor-newdatastore-button]
    
6. Remplacez **accountname** et **accountkey** avec le nom de compte et les valeurs de clé de compte pour votre compte de stockage Azure.

	![Stockage d'objets Blob éditeur JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Voir [référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) pour plus d'informations sur les propriétés JSON.

6. Cliquez sur **déployer** sur la barre d'outils pour déployer le StorageLinkedService. Vérifiez que le message **lié SERVICE créé avec succès** sur la barre de titre.

	![Stockage d'objets Blob éditeur déployer][image-editor-blob-storage-deploy]

### Créer un service lié pour la base de données SQL Azure
1. Dans le **données fabrique éditeur** , cliquez sur **nouveau magasin de données** bouton sur la barre d'outils et sélectionnez **base de données SQL Azure** dans le menu déroulant. Vous devez voir le modèle JSON pour créer le service SQL Azure lié dans le volet droit.

	![Paramètres de SQL Azure Editr][image-editor-azure-sql-settings]

2. Remplacez **nom_serveur**, **databasename**, **username@servername**, et **mot de passe** avec des noms de votre serveur SQL Azure, le base de données, le compte d'utilisateur et le mot de passe. 
3. Cliquez sur **déployer** sur la barre d'outils pour créer et déployer le AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Étape 3: Créer d'entrée et sortie de tables
Dans l'étape précédente, vous avez créé des services liés **StorageLinkedService** et **AzureSqlLinkedService** pour lier un compte de stockage Azure et la base de données SQL Azure à l'usine de données : **ADFTutorialDataFactory**. Dans cette étape, vous allez définir deux tables de fabrique de données-- **EmpTableFromBlob** et **EmpSQLTable** --qui représentent les données d'entrée/sortie sont stockées dans les banques de données référencées par StorageLinkedService et AzureSqlLinkedService respectivement. Pour EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données et pour EmpSQLTable, vous spécifiez la table SQL qui stocke les données de sortie.

### Créer une table d'entrée 
Une table est un jeu de données rectangulaire qui dispose d'un schéma. Dans cette étape, vous allez créer une table nommée **EmpBlobTable** qui pointe vers un conteneur d'objets blob dans le stockage Azure représenté par le **StorageLinkedService** liés de service.

1. Dans le **éditeur** pour la fabrique de données, cliquez sur **nouveau dataset** bouton dans la barre d'outils et cliquez sur **table Blob** dans le menu déroulant. 
2. Remplacez JSON dans le volet droit par l'extrait de JSON suivant : 

        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     Notez les points suivants :
	
	- emplacement **type** a **AzureBlobLocation**.
	- **linkedServiceName** a **StorageLinkedService**. Vous avez créé ce service lié à l'étape 2.
	- **folderPath** est défini sur le **adftutorial** conteneur. Vous pouvez également spécifier le nom d'un objet blob dans le dossier. Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.  
	- format **type** est définie sur **FormatTexte**
	- Il existe deux champs dans le fichier texte – **FirstName** et **LastName** – séparés par une virgule (** columnDelimiter **)	
	- Le **disponibilité** est définie sur **toutes les heures** (** fréquence ** a la valeur **heure** et **intervalle** est définie sur **1** ), de sorte que le service de données fabrique recherchera les données d'entrée toutes les heures dans le dossier racine dans le conteneur d'objets blob (** adftutorial **) vous avez spécifié. 
	

	Si vous ne spécifiez pas un **nom de fichier** pour un **d'entrée** **table**, tous les fichiers BLOB à partir du dossier d'entrée (** folderPath **) sont considérés comme entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour obtenir des exemples.
 
	Si vous ne spécifiez pas un **nom de fichier** pour un **table de sortie**, le généré les fichiers dans le **folderPath** sont nommées selon le format suivant : données & lt ; GUID & gt;. txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété **partitionedBy**. Dans l'exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]Voir [référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) pour plus d'informations sur les propriétés JSON.

2. Cliquez sur **déployer** sur la barre d'outils pour créer et déployer le **EmpTableFromBlob** table. Vérifiez que le **TABLE créée avec succès** message sur la barre de titre de l'éditeur.

### Créer la table de sortie
Dans cette partie de l'étape, vous allez créer une table de sortie nommée **EmpSQLTable** qui pointe vers une table SQL dans SQL Azure base de données qui est représenté par le **AzureSqlLinkedService** liés de service.

1. Dans le **éditeur** pour la fabrique de données, cliquez sur **nouveau dataset** bouton dans la barre d'outils et cliquez sur **table SQL Azure** dans le menu déroulant. 
2. Remplacez JSON dans le volet droit par l'extrait de JSON suivant :

        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSqlTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "AzureSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Notez les points suivants :
	
	* emplacement **type** a **AzureSQLTableLocation**.
	* **linkedServiceName** a **AzureSqlLinkedService** (vous avez créé ce service lié à l'étape 2).
	* **tablename** est définie sur **emp**.
	* Il existe trois colonnes – **ID**, **FirstName**, et **LastName** – dans la table emp dans la base de données, mais l'ID est une colonne d'identité, vous devez spécifier uniquement **FirstName** et **LastName** ici.
	* Le **disponibilité** est définie sur **toutes les heures** (** fréquence ** valeur **heure** et **intervalle** la valeur **1**). Le service de données fabrique génère une tranche de données de sortie toutes les heures dans les **emp** table dans la base de données SQL Azure.


3. Cliquez sur **déployer** sur la barre d'outils pour créer et déployer le **EmpSQLTable** table.


## <a name="CreateAndRunAPipeline"></a>Étape 4: Créer et exécuter un pipeline
Dans cette étape, vous créez un pipeline avec une **activité copie** qui utilise **EmpTableFromBlob** en tant qu'entrée et **EmpSQLTable** en tant que sortie.

1. Dans le **éditeur** pour la fabrique de données, cliquez sur **nouveau pipeline** dans la barre d'outils. Si ce bouton n’est pas affiché dans la barre d’outils, cliquez sur **... (points de suspension)**. Vous pouvez également cliquer sur **Pipelines** dans l'arborescence, puis sur **Nouveau pipeline**.

	![Éditeur de nouveau bouton de Pipeline][image-editor-newpipeline-button]
 
2. Remplacez JSON dans le volet droit par l'extrait de JSON suivant :

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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
        		],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Notez les points suivants :

	- Dans la section des activités, il n'existe qu'une seule activité dont **type** a **CopyActivity**.
	- Entrée de l'activité est définie sur **EmpTableFromBlob** et de sortie de l'activité est définie sur **EmpSQLTable**.
	- Dans le **transformation** section, **BlobSource** est spécifié comme type de source et **SqlSink** est spécifié comme type de récepteur.

	> [AZURE.NOTE]Remplacez la valeur de la **Démarrer** propriété avec la date du jour et **fin** valeur avec le jour suivant. Vous pouvez spécifier uniquement la partie de date et ignorer la partie heure de l'heure. Par exemple, « 2015-02-03 », qui est équivalent à « 2015-02-03T00:00:00Z » à la fois début et dates/heures de fin doivent être dans [au format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. Le **fin** temps est facultative, mais nous allons l'utiliser dans ce didacticiel. Si vous ne spécifiez pas de valeur pour le **fin** propriété, il est calculé en tant que « ** début + 48 heures ** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour le **fin** propriété. Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
	> [AZURE.NOTE]Voir [référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) pour plus d'informations sur les propriétés JSON.

4. Cliquez sur **déployer** sur la barre d'outils pour créer et déployer le **ADFTutorialPipeline**. Vérifiez que le **PIPELINE créé avec succès** message.
5. Maintenant, fermez le **éditeur** lame en cliquant sur **X**. Cliquez sur **X** à nouveau pour fermer le volet ADFTutorialDataFactory avec la vue arborescence et la barre d'outils. Si vous voyez **vos modifications non enregistrées seront ignorées** cliquez sur **OK**.
6. Vous devez être à la **DATA FACTORY** lame pour le **ADFTutorialDataFactory**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.
 
### Vue de la fabrique de données dans une vue de diagramme 
1. Dans le **DATA FACTORY** lame, cliquez sur **diagramme**.

	![Fabrique lame - diagramme mosaïque][image-datafactoryblade-diagramtile]

2. Le diagramme ressemble à ce qui suit :

	![Vue de diagramme][image-data-factory-get-started-diagram-blade]

	Vous pouvez faire un zoom, un zoom arrière, zoom à 100 %, zoom pour ajuster automatiquement positionner les pipelines et les tables et afficher les informations de lignage (met en surbrillance des éléments en amont et en aval des éléments sélectionnés). Vous pouvez double-blick sur un objet (table d'entrée/sortie ou pipeline) pour afficher les propriétés pour celle-ci. 
3. Avec le bouton droit sur **ADFTutorialPipeline** dans la vue de diagramme et cliquez sur **ouverte**. Vous devez voir les activités dans le pipeline, ainsi que des groupes de données d'entrée et de sortie pour les activités. Dans ce didacticiel, vous n'avez qu'une seule activité dans le pipeline (activité copie) avec EmpTableBlob en tant que dataset d'entrée et EmpSQLTable comme dataset de sortie.   

	![Ouverte](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Cliquez sur **Data factory** dans l'arborescence de navigation dans le coin supérieur gauche pour revenir à la vue de diagramme. La vue de diagramme affiche tous les pipelines. Dans cet exemple, vous avez seulement créé un pipeline.
 

## <a name="MonitorDataSetsAndPipeline"></a>Étape 5: Surveiller les groupes de données et le pipeline
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour plus d'informations sur l'utilisation des applets de commande pour l'analyse, consultez [analyse et gestion de la fabrique de données à l'aide de PowerShell Cmdlets][monitor-manage-using-powershell].

1. Accédez à [portail Azure (aperçu)][azure-preview-portal] si vous ne l'avez ouverte. 
2. Si le volet de **ADFTutorialDataFactory** est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialDataFactory** sur la **tableau d'accueil**. 
3. Vous devez voir le nombre et les noms des tables et du pipeline que vous avez créés sur ce panneau.

	![page d'accueil avec des noms][image-data-factory-get-started-home-page-pipeline-tables]

4. Maintenant, cliquez sur **Datasets** vignette.
5. Dans le **Datasets** lame, cliquez sur **EmpTableFromBlob**. C'est la table d'entrée pour le **ADFTutorialPipeline**.

	![Groupes de données avec EmpTableFromBlob sélectionné][image-data-factory-get-started-datasets-emptable-selected]   
5. Notez que les tranches de données à l'heure actuelle ont déjà été produits et qu'ils sont **prêt** parce que le **emp.txt** fichier existe tout le temps dans le conteneur d'objets blob : **adftutorial\\input**. Confirmez qu'aucune coupe n'apparaît dans le **Échec récemment tranches** section en bas.

	Les deux **récemment mis à jour les tranches** et **Échec récemment tranches** listes sont triés par le **heure de dernière mise à jour**. L'heure de mise à jour d'une tranche de données est modifié dans les situations suivantes.
    

	-  Vous mettre à jour l'état de la coupe manuellement, par exemple, à l'aide de la **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **exécuter** sur la **tranche** lame pour la tranche.
	-  La tranche modifie l'état en raison d'une exécution (par exemple, une exécution démarré, une exécution s'est terminée et a échoué, une exécution s'est terminée et réussie, etc.).
 
	Cliquez sur le titre des listes ou des **... (points de suspension)** Pour consulter la liste supérieure des tranches. Cliquez sur **filtre** sur la barre d'outils pour filtrer les secteurs.
	
	Pour afficher les tranches de données triés par les heures de début et de fin de tranche au lieu de cela, cliquez sur **tranches de données (par heure de tranche)** vignette.

	![Tranches de données par heure de secteur][DataSlicesBySliceTime]

6. Maintenant, dans le **Datasets** lame, cliquez sur le **EmpSQLTable**. C'est la table de sortie pour le **ADFTutorialPipeline**.

	![lame de jeux de données][image-data-factory-get-started-datasets-blade]



	 
6. Vous devez voir les **EmpSQLTable** lame comme indiqué ci-dessous :

	![lame de table][image-data-factory-get-started-table-blade]
 
7. Notez que les tranches de données à l'heure actuelle ont déjà été produits et qu'ils sont **prêt**. Aucune coupe n'apparaît dans le **tranches problème** section en bas.
8. Cliquez sur **... (Sélection)** Pour afficher tous les secteurs.

	![lame de tranches de données][image-data-factory-get-started-dataslices-blade]

9. Cliquez sur un secteur de données à partir de la liste et vous devez voir les **tranche de données** lame.

	![lame de tranche de données][image-data-factory-get-started-dataslice-blade]
  
	Si la section n'est pas dans le **prêt** état, vous pouvez voir les tranches en amont qui ne sont pas prêts et bloquent la tranche actuelle à partir de l'exécution dans le **tranches en amont qui ne sont pas prêts** liste.

11. Dans le **tranche de données** lame, vous devriez voir toute l'activité s'exécute dans la liste du bas. Cliquez sur une **activité exécuter** pour voir les **Détails de l'exécution activité** lame.

	![Activité exécuter des détails][image-data-factory-get-started-activity-run-details]

	
12. Cliquez sur **X** pour fermer toutes les lames jusqu'à ce que vous reveniez à la lame de base pour le **ADFTutorialDataFactory**.
14. (facultatif) Cliquez sur **Pipelines** sur la page d'accueil **ADFTutorialDataFactory**, cliquez sur **ADFTutorialPipeline** dans les **Pipelines** lame et extraction des tables d'entrée (** consommé **) ou tables de sortie (** produit **).
15. Lancez **SQL Server Management Studio**, connectez-vous à la base de données SQL Azure et vérifiez que les lignes sont insérées dans le **emp** table dans la base de données.

	![résultats de la requête SQL][image-data-factory-get-started-sql-query-results]


## Résumé 
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour copier des données d'objet blob Azure dans une base de données SQL Azure. La version préliminaire du portail Azure vous permet de créer la fabrique de données, les services liés, les tables et un pipeline. Voici les étapes de premier niveau que vous avez effectuées dans ce didacticiel :

1.	Créer un Azure **fabrique données**.
2.	Créer **lié services** lier des magasins de données et calcule (appelés **Services liés**) à la fabrication de données.
3.	Créer **tableaux** qui décrivent entrée des données et les données de sortie pour les pipelines.
4.	Créer **pipelines**. Un pipeline comprend une ou plusieurs activités. Il traite des entrées et produit des sorties. Définir la période active pour le pipeline en spécifiant **Démarrer** temps et **fin** temps pour le pipeline. La période active définit le délai pendant lequel les tranches de données seront créées. 


> [AZURE.NOTE]Pour obtenir la liste des activités prises en charge, consultez [Pipelines et les activités][msdn-activities] rubrique et pour une liste des services liés pris en charge, consultez [Services liés][msdn-linkedservices] rubrique dans MSDN Library.
> 
> Pour effectuer ce didacticiel à l'aide d'Azure PowerShell, consultez [Create et analyse une fabrique de données à l'aide d'Azure PowerShell][monitor-manage-using-powershell].

## Étapes suivantes

Article | Description
------ | ---------------
[Copier des données avec Azure Data Factory - activité de copie][copy-activity] | Cet article fournit une description détaillée de la **activité copie** vous avez utilisé dans ce didacticiel. 
[Activer vos pipelines travailler avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas qui montre comment copier des données d'un **base de données SQL Server sur site** à un objet blob Azure. 
[Didacticiel : Déplacer et traiter des fichiers journaux à l'aide de la fabrique de données][adf-tutorial] | Cet article fournit une **procédure pas à pas de bout en bout** qui montre comment implémenter un **scénario réel** à l'aide de la fabrique de données Azure pour transformer les données à partir des fichiers journaux dans les analyses.
[Résoudre les problèmes de la fabrique de données][troubleshoot] | Cet article décrit comment **dépanner** émet de la fabrique de données Azure. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence du développeur fabrique données Azure][developer-reference] | La référence du développeur possède le contenu de référence complet pour les applets de commande, script JSON, fonctions, etc.... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 