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
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Prise en main d'Azure Data Factory
Cet article vous permet de commencer à utiliser Azure Data Factory. Le didacticiel de cet article vous montre comment créer une fabrique de données Azure, et un pipeline dans la fabrique de données pour copier des exemples de données d'un stockage d'objets blob Azure dans une base de données SQL Azure.

La liste suivante énumère les étapes classiques que les développeurs doivent effectuer : 

1.	Créez une **fabrique de données Azure**.
2.	Créez **des services liés** pour lier des magasins de données et des services de calcul à la fabrique de données.  Par exemple, un service lié peut lier une base de données SQL Azure ou un cluster HDInsight à la fabrique de données.
3.	Créez des **tables** qui décrivent les données d'entrée et les données de sortie pour les pipelines. Les tables spécifient également l'emplacement réel des données dans les magasins de données liés à une fabrique de données. Par exemple, une table peut spécifier le nom de la table SQL dans une base de données SQL Azure ou dans un conteneur d'objets blob d'un objet blob Azure. 
4.	Créez des **pipelines**. Un pipeline est composé d'une ou plusieurs activités qui consomment des données d'entrée et produisent des données de sortie. Une activité de copie se charge de copier les données d'un magasin de données source vers un magasin de données cible. Par ailleurs, une activité HDInsight traite les données d'entrée à l'aide de scripts Hive/Pig.  
5.	Spécifiez **la période active** pour les pipelines (pour le traitement des données). La période active définit le délai pendant lequel les tranches de données sont créées. Vous pouvez spécifier la date/l'heure de début et la date/l'heure de fin pour un pipeline, ou vous pouvez les exécuter en permanence.

Ce didacticiel présente les procédures suivantes : 

1.	Utilisez le **portail Azure en version préliminaire** pour créer une fabrique de données Azure et les services liés pour les magasins de données.
2.	Utilisez **Azure PowerShell** pour créer des tables et un pipeline. Le portail ne prend pas en charge la création des tables et des pipelines dans cette version


##Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Un abonnement Azure. Pour plus d'informations sur l'obtention d'un abonnement, consultez [Options d'achat] [azure-purchase-options], [Offres spéciales membres][azure-member-offers] ou [Version d'évaluation gratuite][azure-free-trial].
- Téléchargez et installez [Azure PowerShell][download-azure-powershell] sur votre ordinateur.
- Parcourez la rubrique [Présentation d'Azure Data Factory][data-factory-introduction].
- Compte Azure Storage. Vous allez utiliser le stockage d'objets blob comme magasin de données source dans ce didacticiel. Consultez [À propos des comptes de stockage][data-factory-create-storage] pour connaître les étapes de création d'un stockage Azure.
- Base de données SQL Azure. Vous allez créer un exemple de base de données et l'utiliser comme magasin de données cible dans ce didacticiel. Consultez [Comment créer et configurer une base de données SQL Azure][data-factory-create-sql-database] pour connaître les étapes de création d'une base de données SQL Azure.

##Dans ce didacticiel

Étape | Description
-----| -----------
[Étape 1 : créer une fabrique de données Azure](#CreateDataFactory) | Dans cette étape, vous allez créer une fabrique de données Azure nommée **ADFTutorialDataFactory**. 
[Étape 2 : créez des services liés](#CreateLinkedServices) | Dans cette étape, vous allez créer deux services liés : **MyBlobStore** et **MyAzureSQLStore**. MyBlobStore lie un stockage Azure et MyAzureSQLStore lie une base de données SQL Azure à ADFTutorialDataFactory.
[Étape 3 : Création de jeux de données d'entrée et de sortie](#CreateInputAndOutputDataSets) | Dans cette étape, vous allez définir deux jeux de données (**EmpTableFromBlob** et **EmpSQLTable**), qui sont utilisés en entrée et sortie de l'**activité de copie** du pipeline ADFTutorialPipeline que vous allez créer à l'étape suivante.
[Étape 4 : créer et exécuter un pipeline](#CreateAndRunAPipeline) | Dans cette étape, vous allez créer un pipeline nommé **ADFTutorialPipeline**. Le pipeline effectue une **activité de copie** qui se charge de copier des données d'objet blob Azure dans une table de base de données Azure de sortie.
[Étape 5 : surveiller les jeux de données et le pipeline](#MonitorDataSetsAndPipeline) | Dans cette étape, vous allez surveiller les jeux de données et le pipeline à l'aide d'Azure Management Studio.
 


## <a name="CreateDataFactory"></a>Étape 1 : créer une fabrique de données Azure
Dans cette étape, vous utilisez le portail Azure en version préliminaire pour créer une fabrique de données Azure nommée **ADFTutorialDataFactory**.

1.	Une fois connecté au [portail Azure en version préliminaire][azure-preview-portal], cliquez dans le coin inférieur gauche sur **NOUVEAU**, puis sur **Fabrique de données** dans le panneau **Nouveau**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Si **Fabrique de données** ne s'affiche pas dans le panneau **Nouveau**, faites défiler vers le bas.  


6. Dans le panneau **Nouvelle fabrique de données** :
	1. Entrez **ADFTutorialDataFactory** pour le **nom**. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Cliquez sur **NOM DU GROUPE DE RESSOURCES** et procédez comme suit :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, entrez **ADFTutorialResourceGroup** comme **nom** du groupe de ressources, puis cliquez sur **OK**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Si vous utilisez un autre groupe de ressources, vous devrez utiliser le groupe de ressources que vous sélectionnez ici à la place d'ADFTutorialResourceGroup.  
7. Dans le panneau **Nouvelle fabrique de données**, notez qu'**Ajouter au tableau d'accueil** est sélectionné.
8. Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.

	> [WACOM.NOTE] Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l'erreur : **Le nom de la fabrique de données " ADFTutorialDataFactory " n'est pas disponible**, changez le nom (par exemple, votrenomADFTutorialDataFactory). Utilisez ce nom à la place d'ADFTutorialFactory quand vous effectuez les étapes de ce didacticiel.  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Cliquez sur le hub **NOTIFICATIONS** à gauche et recherchez les notifications relatives au processus de création.
10. Une fois la création terminée, vous verrez le panneau de fabrique de données comme indiqué ci-dessous
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. Vous pouvez également l'ouvrir dans le **Tableau d'accueil** comme indiqué ci-dessous en cliquant sur **ADFTutorialFactory** 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Étape 2 : créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un emplacement Azure Storage, une base de données SQL Azure ou une base de données SQL Server locale.

Dans cette étape, vous allez créer deux services liés : **MyBlobStore** et **MyAzureSQLStore**. Le service lié MyBlobStore se charge de lier un compte Azure Storage, et MyAzureSQLStore lie une base de données SQL Azure à **ADFTutorialDataFactory**. Vous allez créer un pipeline plus loin dans ce didacticiel pour copier les données d'un conteneur d'objets blob dans MyBlobStore vers une table SQL dans MyAzureSQLStore.

### Créer un service lié pour un compte de stockage Azure
1.	Dans le panneau **FABRIQUE DE DONNÉES**, cliquez sur la vignette **Services liés** pour lancer le panneau **Services liés**.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. Dans le panneau **Services liés**, cliquez sur **Ajouter un magasin de données**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. Dans le panneau **Nouveau magasin de données** :  
	1. Entrez le **nom** du magasin de données. Dans le contexte de ce didacticiel, entrez **MyBlobStore** comme **nom**.
	2. Entrez la **description** (facultative) du magasin de données.
	3. Cliquez sur **Type**.
	4. Sélectionnez le **compte de stockage Azure**, puis cliquez sur **OK**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  Vous revenez à présent dans le panneau **Nouveau magasin de données**, qui se présente comme suit :

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Entrez le **Nom de compte** et la **Clé de compte** de votre compte Azure Storage, puis cliquez sur **OK**.   

6. Après avoir cliqué sur **OK** dans le panneau **Nouveau magasin de données**, vous devez voir **myblobstore** dans la liste des **MAGASINS DE DONNÉES** du panneau **Services liés**. Vérifiez la présence d'éventuels messages dans le hub **NOTIFICATIONS** (à gauche).

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Créer un service lié pour une base de données SQL Azure
1. Dans le panneau **Services liés**, cliquez sur **Ajouter un magasin de données** dans la barre d'outils pour ajouter une autre source de données (base de données SQL Azure).
2. Dans le panneau Nouveau magasin de données :
	1. Entrez le **nom** du magasin de données. Dans le contexte de ce didacticiel, entrez **MyAzureSQLStore** comme **NOM**. 
	2. Entrez une **DESCRIPTION** (facultative) du magasin.
	3. Cliquez sur **Type**, puis sélectionnez **Base de données SQL Azure**.
3. Entrez un **Serveur**, une **Base de données**, un **Nom d'utilisateur** et un **Mot de passe** pour la base de données SQL Azure, puis cliquez sur **OK**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. Après avoir cliqué sur **OK** dans le panneau **Nouveau magasin de données**, vous devez voir les deux magasins dans le panneau **Services liés**

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Étape 3 : créer des tables d'entrée et de sortie

À l'étape précédente, vous avez créé les services liés **MyBlobStore** et **MyAzureSQLStore** pour lier un compte Azure Storage et la base de données SQL Azure à la fabrique de données : **ADFTutorialDataFactory**. Dans cette étape, vous allez créer des tables qui représentent des données d'entrée et de sortie pour l'activité de copie dans le pipeline, que vous allez créer à l'étape suivante. 

Une table est un jeu de données rectangulaire et il s'agit du seul type de jeu de données qui est pris en charge pour l'instant. La table d'entrée fait référence à un conteneur d'objets blob dans l'emplacement Azure Storage vers lequel pointe MyBlobStore, et la table de sortie fait référence à une table SQL dans la base de données SQL Azure vers laquelle pointe MyAzureSQLStore.  
 
La création de jeux de données et de pipelines n'est pas prise en charge par le portail Azure en version préliminaire à ce stade, vous allez donc utiliser les applets de commande Azure PowerShell pour créer des tables et des pipelines. Avant de créer des tables, vous devez d'abord effectuer les opérations suivantes (les étapes détaillées suivent la liste).

* Créez un conteneur d'objets blob nommé **adftutorial** dans le stockage d'objets blob Azure vers lequel pointe MyBlobStore. 
* Créez et téléchargez un fichier texte, **emp.txt**, en tant qu'objet blob vers le conteneur **adftutorial**. 
* Créez une table nommée **emp** dans la base de données SQL Azure vers laquelle pointe MyAzureSQLStore.
* Créez un dossier nommé **ADFGetStarted** sur votre disque dur.  

### Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
1. Lancez le Bloc-notes, collez le texte suivant, puis enregistrez-le sous le nom **emp.txt** dans le dossier **C:\ADFGetStarted** sur votre disque dur. 

        John, Doe
		Jane, Doe
				
2. Utilisez des outils tels que l'[Explorateur Azure Storage](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et télécharger le fichier **emp.txt** vers ce dernier.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données SQL Azure. Vous pouvez utiliser la console de gestion SQL Azure pour vous connecter à une base de données SQL Azure et exécuter le script SQL. Vous pouvez également utiliser SQL Server Management Studio pour effectuer cette tâche. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Pour lancer la console de gestion SQL Azure, cliquez sur **GÉRER**, comme indiqué dans l'image suivante :
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Créer une table d'entrée 
Une table est un jeu de données rectangulaire qui dispose d'un schéma. Dans cette étape, vous allez créer une table nommée **EmpBlobTable** qui pointe vers un conteneur d'objets blob dans l'emplacement Azure Storage représenté par le service lié **MyBlobStore**.


1. Créez un fichier JSON pour une table Data Factory qui représente des données dans le fichier emp.txt de l'objet blob. Lancez le Bloc-notes, copiez le script JSON suivant puis enregistrez-le sous EmpBlobTable.json dans le dossier C:\ADFGetStarted.


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
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitonexternal": {}
       		 	}
    		}
		}

		
     Notez les points suivants : 
	
	- Le **type** d'emplacement a la valeur **AzureBlobLocation**.
	- **linkedServiceName** a la valeur **MyBlobStore**. Vous avez créé ce service lié à l'étape 2).
	- **folderPath** a la valeur du conteneur **adftutorial**. Vous pouvez également spécifier le nom d'un objet blob dans le dossier. Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.  
	- Le **type** de format a la valeur **TextFormat**
	- - Il existe deux champs dans le fichier texte, **FirstName** et **ColumnName**, séparés par une virgule (columDelimiter)	
	- La **disponibilité** est définie **en heure** (la **fréquence** a la valeur **hour** et l'**intervalle** a la valeur **1**). Le service Data Factory recherche les données d'entrée toutes les heures dans le dossier d'entrée du conteneur d'objets blob (**adftutorial**) que vous avez spécifié.

	Si vous ne spécifiez pas de **fileName** pour une **table d'entrée**, tous les fichiers/objets blob du répertoire d'entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour plus d'informations.
 
	Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété partitionedBy. Dans l'exemple suivant, folderPath utilise l'année, le mois et le jour à partir de SliceStart (heure de début de la tranche en cours de traitement) et fileName utilise Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Lancez **Azure PowerShell** et exécutez la commande suivante pour passer au mode **AzureResourceManager**. Les applets de commande Azure Data Factory sont disponibles en mode **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- Exécutez **Add-AzureAccount**, puis entrez les mêmes nom d'utilisateur et mot de passe que ceux utilisés pour la connexion au portail Azure en version préliminaire.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l'abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire.

3. Utilisez l'applet de commande **New-AzureDataFactoryTable** pour créer la table d'entrée à l'aide du fichier **EmpBlobTable.json**.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	Consultez la rubrique [Référence des applets de commande Data Factory][cmdlet-reference] pour plus d'informations sur cette applet de commande et d'autres applets de commande Data Factory.
 
### Créer la table de sortie
Dans cette partie de l'étape, vous allez créer une table de sortie nommée **EmpSQLTable** qui pointe vers une table SQL de la base de données SQL Azure, représentée par le service lié **MyAzureSQLStore**. 

1. Créez un fichier JSON pour une table Data Factory qui représente des données dans la base de données SQL Azure. Lancez le Bloc-notes, copiez le script JSON suivant, puis enregistrez-le sous le nom **EmpSQLTable.json** dans le dossier **C:\ADFGetStarted**.



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
            		"type": "AzureSQLTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Notez les points suivants : 
	
	* Le **type** d'emplacement a la valeur **AzureSQLTableLocation**.
	* **linkedServiceName** a la valeur **MyAzureSQLStore** (vous avez créé ce service lié à l'étape 2).
	* **tablename** a la valeur **emp**.
	* Il existe trois colonnes (**ID**, **FirstName** et **LastName**) dans la table emp de la base de données. Toutefois, ID est une colonne d'identité, vous devez donc spécifier uniquement **FirstName** et **LastName** ici.
	* La **disponibilité** est définie **en heure** (la fréquence a la valeur hour et l'intervalle a la valeur 1).  Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Azure.


2. Dans **Azure PowerShell**, exécutez la commande suivante pour créer une autre table Data Factory représentant la table **emp** dans la base de données SQL Azure.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Étape 4 : créer et exécuter un pipeline
Dans cette étape, vous créez un pipeline avec une **activité de copie** qui utilise **EmpTableFromBlob** en entrée et **EmpSQLTable** en sortie.

1. Créez un fichier JSON pour le pipeline. Lancez le Bloc-notes, copiez le script JSON suivant, puis enregistrez-le sous le nom **ADFTutorialPipeline.json** dans le dossier **C:\ADFGetStarted**.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
      		}
		} 

	Notez les points suivants :

	- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **CopyActivity**.
	- L'entrée de l'activité a la valeur **EmpTableFromBlob** et la sortie de l'activité a la valeur **EmpSQLTable**.
	- Dans la section **transformation**, **BlobSource** est spécifié en tant que type de source et **SqlSink** est spécifié en tant que type de récepteur.

2. Utilisez l'applet de commande **New-AzureDataFactoryPipeline** pour créer un pipeline à l'aide du fichier **ADFTutorialPipeline.json** que vous avez créé.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. Une fois les pipelines créés, vous pouvez spécifier la durée de traitement des données. En spécifiant la période active pour un pipeline, vous définissez la durée pendant laquelle les tranches de données seront traitées selon les propriétés de disponibilité qui ont été définies pour chaque table Azure Data Factory.  Exécutez la commande PowerShell suivante pour définir la période active sur le pipeline, puis entrez Y pour confirmer. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Remplacez la valeur **StartDateTime** par le jour actuel et la valeur **EndDateTime** par le jour suivant. Les valeurs StartDateTime et EndDateTime sont exprimées au format horaire UTC, et doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. **EndDateTime** est facultatif, mais nous allons l'utiliser dans ce didacticiel. 
	Si vous ne spécifiez pas la valeur **EndDateTime**, elle est calculée comme suit : **StartDateTime + 48 heures**. Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** pour **EndDateTime**.  
	
	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.

4. Dans le **portail Azure**, dans le panneau **FABRIQUE DE DONNÉES** pour **ADFTutorialDataFactory**, cliquez sur **Diagramme**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Le diagramme ressemble à ce qui suit : (double-cliquez sur un titre pour afficher des détails sur l'objet représenté par le panneau).

	![Diagram view][image-data-factory-get-started-diagram-blade]

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline. En outre, vous avez planifié le pipeline.

## <a name="MonitorDataSetsAndPipeline"></a>Étape 5 : surveiller les jeux de données et le pipeline
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour plus d'informations sur l'utilisation des applets de commande pour la surveillance, consultez [Surveillance et gestion de Data Factory à l'aide des applets de commande PowerShell][monitor-manage-using-powershell].

1. Accédez au [portail Azure (en version préliminaire)][azure-preview-portal] s'il n'est pas déjà ouvert. 
2. Si le panneau pour **ADFTutorialDataFactory** n'est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialDataFactory** sur le **Tableau d'accueil**. 
3. Vous devez voir le nombre et les noms de tables et de pipelines que vous avez créés sur ce panneau.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Cliquez maintenant sur la vignette **Jeux de données**.
5. Dans le panneau **Jeux de données**, cliquez sur **EmpTableFromBlob**. Il s'agit de la table d'entrée pour **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Notez que les tranches de données jusqu'à l'heure actuelle ont déjà été produites et sont **prêtes**, car le fichier **emp.txt** existe en permanence dans le conteneur d'objets blob : **adftutorial\input**. Vérifiez qu'aucune tranche n'apparaît dans la section **Tranches problématiques** en bas.
6. À présent, dans le panneau **Jeux de données**, cliquez sur la table **EmpSQLTable**. Il s'agit de la table de sortie pour **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Vous devez voir le panneau **EmpSQLTable** comme affiché ci-dessous :

	![table blade][image-data-factory-get-started-table-blade]
 
7. Notez que les tranches de données jusqu'à l'heure actuelle ont déjà été produites et sont **prêtes**. Aucune tranche n'apparaît dans la section **Tranches problématiques** en bas.
8. Cliquez sur **... (points de suspension)** pour voir toutes les tranches.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Cliquez sur une tranche de données dans la liste pour voir le panneau **TRANCHE DE DONNÉES**.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. Dans le panneau **TRANCHE DE DONNÉES**, vous devez voir toute l'activité qui s'exécute dans la liste en bas. Cliquez sur une **exécution d'activité** pour voir le panneau **DÉTAILS D'EXÉCUTION DE L'ACTIVITÉ**. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Cliquez sur **X** pour fermer tous les panneaux et revenir au panneau d'accueil d'**ADFTutorialDataFactory**.
14. (facultatif) Cliquez sur **Pipelines** dans la page d'accueil d'**ADFTutorialDataFactory**, cliquez sur **ADFTutorialPipeline** dans le panneau **Pipelines**, puis extrayez les tables d'entrée (**Consommées**) ou les tables de sortie (**Produites**).
15. Lancez **SQL Server Management Studio**, connectez-vous à la base de données SQL Azure, puis vérifiez les lignes insérées dans la table **emp** de la base de données.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Résumé 
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour copier des données d'objet blob Azure dans une base de données SQL Azure. Vous avez utilisé le portail Azure en version préliminaire pour créer la fabrique de données et les services liés. Vous avez utilisé les applets de commande PowerShell Azure pour créer des tables et un pipeline, puis vous avez programmé le pipeline. Voici les étapes de premier niveau que vous avez effectuées dans ce didacticiel :  

1.	Créer une **fabrique de données** Azure.
2.	Créer des **services liés** qui lient des magasins de données et des calculs (appelés **Services liés**) vers la fabrique de données.
3.	Créer des **tables** qui décrivent les données d'entrée et les données de sortie des pipelines.
4.	Créer des **pipelines**. Un pipeline comprend une ou plusieurs activités. Il traite des entrées et produit des sorties. 
5.	Spécifier **la période active** pour les pipelines (pour le traitement des données). La période active définit le délai pendant lequel les tranches de données seront créées.

## Étapes suivantes

Article | Description
------ | ---------------
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory][adf-tutorial] | Cet article comporte une **procédure pas à pas de bout en bout** qui montre comment implémenter un **scénario réel** à l'aide d'Azure Data Factory pour transformer des données de fichiers journaux en informations pertinentes.
[Copie de données avec Azure Data Factory (activité de copie)][copy-activity] | Cet article fournit une description détaillée de l'**activité de copie** que vous avez utilisée dans ce didacticiel.
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas qui permet de copier les données d'une **base de données SQL Server locale** vers un objet blob Azure.
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas qui permet d'exécuter un script **hive/pig** à l'aide de l'**activité HDInsight** pour traiter des données d'entrée et produire des données de sortie.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas qui permet de créer une **activité personnalisée** et de l'utiliser dans un pipeline. 
[Surveillance et gestion d'Azure Data Factory à l'aide de PowerShell][monitor-manage-using-powershell] | Cet article décrit comment **surveiller et gérer** une fabrique de données Azure à l'aide des **applets de commande Azure PowerShell**. Vous pouvez essayer les exemples de l'article portant sur ADFTutorialDataFactory.
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment **résoudre** des problèmes liés à Azure Data Factory. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence des applets de commande Azure Data Factory][cmdlet-reference] | Ces informations de référence comportent des détails sur toutes les **applets de commande Data Factory**.
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les applets de commande, le script JSON, les fonctions, etc. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
