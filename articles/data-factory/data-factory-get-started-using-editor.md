<properties 
	pageTitle="Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Data Factory Editor"
	description="Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l'aide de Data Factory Editor dans le portail Azure."
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
	ms.date="08/25/2015"
	ms.author="spelluru"/>

# Didacticiel : Créer un pipeline avec l'activité de copie à l'aide de Data Factory Editor
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)



##Dans ce didacticiel
Ce didacticiel comprend les étapes suivantes :

Étape | Description
-----| -----------
[Étape 1 : créer une fabrique de données Azure](#CreateDataFactory) | Dans cette étape, vous allez créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.  
[Étape 2 : créer des services liés](#CreateLinkedServices) | Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. StorageLinkedService lie le stockage Azure et AzureSqlLinkedService lie la base de données SQL Azure à ADFTutorialDataFactory. Les données d'entrée pour le pipeline se trouvent dans un conteneur d'objets blob dans le stockage d'objets blob Azure et les données de sortie sont stockées dans une table dans la base de données SQL Azure. Par conséquent, vous ajoutez ces deux magasins de données en tant que services liés à la fabrique de données.      
[Étape 3 : créer des tables d'entrée et de sortie](#CreateInputAndOutputDataSets) | Dans l'étape précédente, vous avez créé des services liés qui font référence à des magasins de données contenant des données d'entrée/sortie. Dans cette étape, vous allez définir deux tables de fabrique de données, **EmpTableFromBlob** et **EmpSQLTable**, qui représentent les données d'entrée/sortie qui sont stockées dans les magasins de données. Pour la table EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données ; pour la table EmpSQLTable, vous spécifiez la table SQL qui stocke les données de sortie. Vous devez également spécifier d'autres propriétés telles que la structure des données, la disponibilité des données, etc. 
[Étape 4 : créer et exécuter un pipeline](#CreateAndRunAPipeline) | Dans cette étape, vous allez créer un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données ADFTutorialDataFactory. Le pipeline effectue une **activité de copie** qui copie les données d'entrée de l'objet blob Azure vers la table SQL Azure de sortie.
[Étape 5 : surveiller les tranches et le pipeline](#MonitorDataSetsAndPipeline) | Dans cette étape, vous allez surveiller les tranches de tables d'entrée et de sortie à l'aide du portail Azure en version préliminaire.
 

## <a name="CreateDataFactory"></a>Étape 1 : créer une fabrique de données Azure
Dans cette étape, vous utilisez le portail Azure en version préliminaire pour créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.

1.	Une fois connecté au [portail Azure en version préliminaire][azure-preview-portal], cliquez dans le coin inférieur gauche sur **NOUVEAU**, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Fabrique de données** dans le panneau **Analyse de données**. 

	![Nouveau -> DataFactory][image-data-factory-new-datafactory-menu]

6. Dans le panneau **Nouvelle fabrique de données** :
	1. Entrez **ADFTutorialDataFactory** comme **nom**. 
	
  		![Panneau Nouvelle fabrique de données][image-data-factory-getstarted-new-data-factory-blade]
	2. Cliquez sur **NOM DU GROUPE DE RESSOURCES** et procédez comme suit :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, entrez **ADFTutorialResourceGroup** comme **nom** du groupe de ressources, puis cliquez sur **OK**. 

			![Créer un groupe de ressources][image-data-factory-create-resource-group]

		Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](resource-group-overview.md).  
7. Dans le panneau **Nouvelle fabrique de données**, notez que l'option **Ajouter au tableau d'accueil** est sélectionnée.
8. Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.

	Le nom de la fabrique de données Azure doit être un nom global unique. Si l'erreur suivante s'affiche, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory), puis tentez de la recréer : **Le nom de la fabrique de données « ADFTutorialDataFactory » n'est pas disponible**. Utilisez ce nom à la place d'ADFTutorialFactory quand vous effectuez les étapes restantes de ce didacticiel. Consultez la rubrique [Data Factory - Règles d'affectation des noms][data-factory-naming-rules] pour savoir comment nommer les artefacts Data Factory.
	 
	![Nom de la fabrique de données indisponible][image-data-factory-name-not-available]

9. Cliquez sur le hub **NOTIFICATIONS** situé à gauche et recherchez les notifications relatives au processus de création. Cliquez sur **X** pour fermer le panneau **NOTIFICATIONS** si celui-ci est ouvert.
10. Une fois la création terminée, le panneau **FABRIQUE DE DONNÉES** apparaît de la manière suivante :

    ![Page d'accueil Data Factory][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Étape 2 : créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un compte de stockage Azure, une base de données SQL Azure ou une base de données SQL Server locale.

Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. Le service lié StorageLinkedService lie un compte de stockage Azure Storage et AzureSqlLinkedService lie une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**. Vous allez créer un pipeline à une étape ultérieure de ce didacticiel pour copier les données d'un conteneur d'objets blob dans StorageLinkedService vers une table SQL dans AzureSqlLinkedService.

### Créer un service lié pour le compte de stockage Azure
1.	Dans le panneau **FABRIQUE DE DONNÉES**, cliquez sur la vignette **Créer et déployer** pour lancer l'**éditeur** de la fabrique de données.

	![Vignette Créer et déployer][image-author-deploy-tile]

	Pour obtenir une présentation détaillée de Data Factory Editor, consultez la rubrique [Data Factory Editor][data-factory-editor].
	 
5. Dans l’**éditeur**, cliquez sur le bouton **Nouveau magasin de données** de la barre d’outils, puis sélectionnez **Stockage Azure** dans le menu déroulant. Le modèle JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans le volet droit.

	![Éditeur - Bouton Nouveau magasin de données][image-editor-newdatastore-button]
    
6. Remplacez les éléments **nom\_compte** et **clé\_compte** par le nom du compte et les valeurs de clé de compte de votre compte Microsoft Azure Storage.

	![Éditeur - Stockage d’objets blob - JSON][image-editor-blob-storage-json]
	
	Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Cliquez sur l’option **Déployer** de la barre d’outils pour déployer le service lié StorageLinkedService. Vérifiez que le message **SERVICE LIÉ CRÉÉ AVEC SUCCÈS** s’affiche dans la barre de titre.

	![Éditeur - Stockage d'objets blob - Déploiement][image-editor-blob-storage-deploy]

### Création d’un service lié pour la base de données SQL Azure
1. Dans **Data Factory Editor**, cliquez sur le bouton **Nouveau magasin de données** de la barre d'outils, puis sélectionnez **Base de données SQL Azure** dans le menu déroulant. Le modèle JSON pour la création du service lié SQL Azure doit apparaître dans le volet droit.

	![Éditeur - Paramètres SQL Azure][image-editor-azure-sql-settings]

2. Remplacez **servername**, **databasename**, ****username@servername** et **password** par les noms de votre serveur SQL Azure, de la base de données, du compte d'utilisateur et par le mot de passe.
3. Cliquez sur l’option **Déployer** de la barre d’outils pour créer et déployer le service AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Étape 3 : créer des tables d'entrée et de sortie
À l'étape précédente, vous avez créé les services liés **StorageLinkedService** et **AzureSqlLinkedService** pour lier un compte Azure Storage et une base de données SQL Azure à la fabrique de données **ADFTutorialDataFactory**. Dans cette étape, vous allez définir deux tables de fabrique de données, **EmpTableFromBlob** et **EmpSQLTable**, qui représentent les données d'entrée/sortie qui sont stockées dans les magasins de données référencés par StorageLinkedService et AzureSqlLinkedService, respectivement. Pour la table EmpTableFromBlob, vous devez spécifier le conteneur d'objets blob qui contient un objet blob avec la source de données ; pour la table EmpSQLTable, vous devez spécifier la table SQL qui stocke les données de sortie.

### Créer une table d'entrée 
Une table est un jeu de données rectangulaire qui dispose d'un schéma. Dans cette étape, vous allez créer une table nommée **EmpBlobTable** qui pointe vers un conteneur d'objets blob dans l'emplacement Azure Storage représenté par le service lié **StorageLinkedService**.

1. Dans l'**éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** dans la barre d'outils et cliquez sur **Table d'objets blob** dans le menu déroulant. 
2. Remplacez le script JSON dans le volet droit par l'extrait de code JSON suivant : 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
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
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Notez les points suivants :
	
	- Le **type** de jeu de données est défini sur **AzureBlob**.
	- **linkedServiceName** a la valeur **StorageLinkedService**. Vous avez créé ce service lié à l'étape 2.
	- **folderPath** a la valeur du conteneur **adftutorial**. Vous pouvez également spécifier le nom d'un objet blob dans le dossier. Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.  
	- Le **type** de format a la valeur **TextFormat**.
	- Il existe deux champs dans le fichier texte, **FirstName** et **LastName**, séparés par une virgule (**columnDelimiter**).	
	- **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** est défini sur **1**), de sorte que le service Data Factory recherche les données d'entrée toutes les heures dans le dossier racine du conteneur d'objets blob (**adftutorial**) que vous avez spécifié. 
	

	Si vous ne spécifiez pas de nom (**fileName**) pour une **table** d'**entrée**, tous les fichiers/objets blob du dossier d'entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour plus d'informations.
 
	Si vous ne spécifiez pas de nom (**fileName**) pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.&lt;Guid&gt;.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

	Pour plus d'informations sur les propriétés JSON, consultez [Référence sur la création de scripts JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Cliquez sur **Déployer** dans la barre d'outils pour créer et déployer la table **EmpTableFromBlob**. Vérifiez que le message **TABLE CORRECTEMENT CRÉÉE** s'affiche dans la barre de titre de l'éditeur.

### Créer la table de sortie
Dans cette partie de l'étape, vous allez créer une table de sortie nommée **EmpSQLTable** qui pointe vers une table SQL de la base de données SQL Azure, représentée par le service lié **AzureSqlLinkedService**.

1. Dans l'**éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** dans la barre d'outils et cliquez sur **Table SQL Azure** dans le menu déroulant. 
2. Remplacez le script JSON dans le volet droit par l'extrait de code JSON suivant :

		{
		  "name": "EmpSQLTable",
		  "properties": {
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
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Notez les points suivants :
	
	* Le **type** de jeu de données est défini sur **AzureSQLTable**.
	* **linkedServiceName** est défini sur **AzureSqlLinkedService** (vous avez créé ce service lié à l'étape 2).
	* **tablename** a la valeur **emp**.
	* Il existe trois colonnes (**ID**, **FirstName** et **LastName**) dans la table emp de la base de données. Toutefois, ID étant une colonne d'identité, vous devez donc spécifier uniquement **FirstName** et **LastName**.
	* **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** est défini sur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Azure.


3. Cliquez sur **Déployer** dans la barre d'outils pour créer et déployer la table **EmpSQLTable**.


## <a name="CreateAndRunAPipeline"></a>Étape 4 : créer et exécuter un pipeline
Dans cette étape, vous créez un pipeline avec une **activité de copie** qui utilise **EmpTableFromBlob** comme entrée et **EmpSQLTable** comme sortie.

1. Dans l'**éditeur** de Data Factory, cliquez sur le bouton **Nouveau pipeline** dans la barre d'outils. Si ce bouton n'est pas affiché dans la barre d'outils, cliquez sur **... (points de suspension)**. Vous pouvez également cliquer sur **Pipelines** dans l'arborescence, puis sur **Nouveau pipeline**.

	![Éditeur - Bouton Nouveau pipeline][image-editor-newpipeline-button]
 
2. Remplacez le script JSON dans le volet droit par l'extrait de code JSON suivant :
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	Notez les points suivants :

	- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **CopyActivity**.
	- L'entrée de l'activité a la valeur **EmpTableFromBlob** et la sortie de l'activité a la valeur **EmpSQLTable**.
	- Dans la section **transformation**, **BlobSource** est spécifié en tant que type de source et **SqlSink** est spécifié en tant que type de récepteur.

	Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end** par le jour suivant. Si vous le souhaitez, spécifiez uniquement la date et ignorez l'heure. For example, "2015-02-03", which is equivalent to "2015-02-03T00:00:00Z"
	
	Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.
	
	Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour la propriété **end**.
	
	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
	Pour plus d'informations sur les propriétés JSON, consultez [Référence sur la création de scripts JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Cliquez sur **Déployer** dans la barre d'outils pour créer et déployer le pipeline **ADFTutorialPipeline**. Vérifiez que le message **PIPELINE CORRECTEMENT CRÉÉ** s'affiche.
5. Maintenant, fermez le panneau **Éditeur** en cliquant sur **X**. Cliquez de nouveau sur **X** pour fermer le panneau ADFTutorialDataFactory avec la vue d’arborescence et la barre d’outils. Si le message **Vos modifications non enregistrées seront ignorées** s'affiche, cliquez sur **OK**.
6. Vous devez revenir au panneau **FABRIQUE DE DONNÉES** pour **ADFTutorialDataFactory**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.
 
### Afficher une vue schématique d'une fabrique de données 
1. Dans le panneau **FABRIQUE DE DONNÉES**, cliquez sur **Diagramme**.

	![Panneau Fabrique de données - Vignette Diagramme][image-datafactoryblade-diagramtile]

2. Le diagramme ressemble à ce qui suit :

	![Vue schématique][image-data-factory-get-started-diagram-blade]

	Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster, positionner automatiquement les pipelines et les tables, et afficher les informations de lignage (mise en surbrillance des éléments en amont et en aval des éléments sélectionnés). Vous pouvez double-cliquer sur un objet (table ou pipeline d'entrée/de sortie) pour afficher les propriétés associées. 
3. Cliquez avec le bouton droit sur **ADFTutorialPipeline** dans la vue schématique et cliquez sur **Ouvrir le pipeline**. Les activités doivent apparaître dans le pipeline, ainsi que des jeux de données d'entrée et de sortie pour les activités. Dans ce didacticiel, il n'y a qu'une seule activité dans le pipeline (activité de copie), avec EmpTableBlob comme jeu de données d'entrée et EmpSQLTable comme jeu de données de sortie.   

	![Ouvrir un pipeline](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Cliquez sur **Fabrique de données** dans la barre de navigation située dans le coin supérieur gauche pour revenir à la vue schématique. La vue schématique affiche tous les pipelines. Dans cet exemple, vous avez créé un seul pipeline.
 

## <a name="MonitorDataSetsAndPipeline"></a>Étape 5 : surveiller les jeux de données et le pipeline
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour plus d'informations sur l'utilisation des applets de commande pour la surveillance, consultez [Surveiller et gérer Data Factory à l'aide des applets de commande PowerShell][monitor-manage-using-powershell].

1. Accédez au [portail Azure (en version préliminaire)][azure-preview-portal] s'il n'est pas déjà ouvert. 
2. Si le panneau pour **ADFTutorialDataFactory** n'est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialDataFactory** dans le **Tableau d'accueil**. 
3. Vous devez voir le nombre et les noms des tables et du pipeline que vous avez créés sur ce panneau.

	![Page d'accueil avec noms][image-data-factory-get-started-home-page-pipeline-tables]

4. Cliquez maintenant sur la vignette **Jeux de données**.
5. Dans le panneau **Jeux de données**, cliquez sur **EmpTableFromBlob**. Il s'agit de la table d'entrée pour **ADFTutorialPipeline**.

	![Jeux de données avec EmpTableFromBlob sélectionné][image-data-factory-get-started-datasets-emptable-selected]   
5. Notez que les tranches de données jusqu'à l'heure actuelle ont déjà été produites et sont **prêtes**, car le fichier **emp.txt** existe en permanence dans le conteneur d'objets blob **adftutorial\\input**. Vérifiez qu'aucune tranche n'apparaît dans la section inférieure **Tranches ayant échoué récemment**.

	Les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** sont triées en fonction de l'**HEURE DE LA DERNIÈRE MISE À JOUR**. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.
    

	-  Vous mettez à jour l’état de la tranche manuellement, par exemple, à l’aide de **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
	-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).
 
	Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches. Cliquez sur l’option **Filtre** de la barre d’outils pour filtrer les tranches.
	
	Pour afficher les tranches de données triées en fonction des heures de début et de fin de tranche, cliquez sur la vignette **Tranches de données (par heure de tranche)**.

	![Tranches de données par heure de tranche][DataSlicesBySliceTime]

6. À présent, dans le panneau **Jeux de données**, cliquez sur la table **EmpSQLTable**. Il s'agit de la table de sortie pour **ADFTutorialPipeline**.

	![Panneau Jeux de données][image-data-factory-get-started-datasets-blade]



	 
6. Vous devez voir le panneau **EmpSQLTable** tel qu'affiché ci-dessous :

	![Panneau Table][image-data-factory-get-started-table-blade]
 
7. Notez que les tranches de données jusqu'à l'heure actuelle ont déjà été produites et sont **prêtes**. Aucune tranche n'apparaît dans la section inférieure **Tranches problématiques**.
8. Cliquez sur **… (points de suspension)** pour voir toutes les tranches.

	![Panneau Tranches de données][image-data-factory-get-started-dataslices-blade]

9. Cliquez sur une tranche de données dans la liste pour voir le panneau **TRANCHE DE DONNÉES**.

	![Panneau Tranche de données][image-data-factory-get-started-dataslice-blade]
  
	Si la tranche n'a pas l'état **Prêt**, vous pouvez voir les tranches en amont qui ne sont pas prêtes et qui empêchent l'exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**.

11. Dans le panneau **TRANCHE DE DONNÉES**, vous devez voir toutes les exécutions d'activité dans la liste inférieure. Cliquez sur une **exécution d'activité** pour voir le panneau **DÉTAILS D'EXÉCUTION DE L'ACTIVITÉ**.

	![Détails de l'exécution d'activité][image-data-factory-get-started-activity-run-details]

	
12. Cliquez sur **X** pour fermer tous les panneaux et revenir au panneau d'accueil d'**ADFTutorialDataFactory**.
14. (facultatif) Cliquez sur **Pipelines** dans la page d'accueil d'**ADFTutorialDataFactory**, cliquez ensuite sur **ADFTutorialPipeline** dans le panneau **Pipelines** , puis extrayez les tables d'entrée (**Consumed**) ou les tables de sortie (**Produced**).
15. Lancez **SQL Server Management Studio**, connectez-vous à la base de données SQL Azure, puis vérifiez que les lignes sont insérées dans la table **emp** de la base de données.

	![Résultats de la requête SQL][image-data-factory-get-started-sql-query-results]


## Résumé 
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour copier des données d'objet blob Azure dans une base de données SQL Azure. Vous avez utilisé le portail Azure en version préliminaire pour créer la fabrique de données, les services liés, les tables et un pipeline. Voici les étapes de premier niveau que vous avez effectuées dans ce didacticiel :

1.	Créer une **fabrique de données** Azure.
2.	Créer des **services liés** qui lient des magasins de données et des calculs (appelés **Services liés**) à la fabrique de données.
3.	Créer des **tables** qui décrivent les données d'entrée et de sortie des pipelines.
4.	Créer des **pipelines**. Un pipeline comprend une ou plusieurs activités. Il traite des entrées et produit des sorties. Définir la période active pour le pipeline en spécifiant l'heure de **Début** et l'heure de **Fin**. La période active définit le délai pendant lequel les tranches de données seront créées. 


Pour obtenir une liste des activités prises en charge, consultez la rubrique [Pipelines et activités][msdn-activities] et pour obtenir une liste des services liés pris en charge, consultez la rubrique [Services liés][msdn-linkedservices] dans la bibliothèque MSDN.
 
Pour réaliser ce didacticiel en utilisant Azure PowerShell, consultez [Créer et surveiller une fabrique de données à l'aide d'Azure PowerShell][monitor-manage-using-powershell].

## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-get-started-using-editor.md).

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
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
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
 

<!---HONumber=September15_HO1-->