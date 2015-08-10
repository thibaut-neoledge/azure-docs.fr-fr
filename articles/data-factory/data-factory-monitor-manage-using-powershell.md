<properties 
	pageTitle="Didacticiel : copie de données depuis un objet blob Azure vers Azure SQL" 
	description="Découvrez comment utiliser Azure PowerShell pour créer et surveiller des fabriques de données Azure." 
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

# Didacticiel : Création et surveillance d’une fabrique de données à l’aide d’Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


Le didacticiel [Prise en main d’Azure Data Factory][adf-get-started] vous montre comment créer et surveiller une fabrique de données Azure à l’aide du [portail Azure en version préliminaire][azure-preview-portal]. Dans ce didacticiel, vous allez créer et surveiller une fabrique de données Azure à l’aide d’applets de commande Azure PowerShell. Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données à partir d’un objet blob Azure vers une base de données SQL Azure.

> [AZURE.NOTE]cet article ne couvre pas toutes les applets de commande Data Factory. Consultez la [Référence des applets de commande Data Factory][cmdlet-reference] pour obtenir une documentation complète sur les applets de commande Data Factory.

##Composants requis
Outre les conditions préalables répertoriées dans la rubrique Vue d’ensemble du didacticiel, Azure PowerShell doit être installé sur votre ordinateur. Si ce n’est pas déjà fait, téléchargez et installez [Azure PowerShell][download-azure-powershell] sur votre ordinateur.

##Dans ce didacticiel
Le tableau suivant répertorie les étapes que vous allez exécuter dans le cadre de ce didacticiel et leurs descriptions.

Étape | Description
-----| -----------
[Étape 1 : créer une fabrique de données Azure](#CreateDataFactory) | Dans cette étape, vous allez créer une fabrique de données Azure nommée **ADFTutorialDataFactoryPSH**. 
[Étape 2 : créer des services liés](#CreateLinkedServices) | Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. StorageLinkedService relie un stockage Azure et AzureSqlLinkedService relie une base de données SQL Azure à ADFTutorialDataFactoryPSH.
[Étape 3 : créer des jeux de données d’entrée et sortie](#CreateInputAndOutputDataSets) | Dans cette étape, vous allez définir deux jeux de données (\*\*EmpTableFromBlob\*\* et **EmpSQLTable**), qui sont utilisés en entrée et sortie de l’**activité de copie** du pipeline ADFTutorialPipeline que vous allez créer à l’étape suivante.
[Étape 4 : créer et exécuter un pipeline](#CreateAndRunAPipeline) | Dans cette étape, vous allez créer un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données : **ADFTutorialDataFactoryPSH**. Le pipeline effectue une **activité de copie** qui se charge de copier des données d’objet blob Azure dans une table de base de données Azure de sortie.
[Étape 5 : surveiller des jeux de données et le pipeline](#MonitorDataSetsAndPipeline) | Dans cette étape, vous allez surveiller les jeux de données et le pipeline à l’aide d’Azure PowerShell.

## <a name="CreateDataFactory"></a>Étape 1 : créer une fabrique de données Azure
Dans cette étape, vous utilisez Azure PowerShell pour créer une fabrique de données Azure nommée **ADFTutorialDataFactoryPSH**.

1. Lancez **Azure PowerShell** et exécutez les commandes suivantes. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous le fermez puis le rouvrez, vous devez réexécuter ces commandes à nouveau.
	- Exécutez **Add-AzureAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Microsoft Azure en version préliminaire.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire. 
2. Passez en mode **AzureResourceManager**, car les applets de commande Azure Data Factory sont disponibles uniquement dans ce mode.

		Switch-AzureMode AzureResourceManager 
3. Créez un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Si vous utilisez un autre groupe de ressources, vous devrez l’utiliser à la place d’ADFTutorialResourceGroup dans ce didacticiel. 
4. Exécutez l’applet de commande **New-AzureDataFactory** pour créer une fabrique de données nommée : **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche : **Le nom de la fabrique de données « ADFTutorialDataFactoryPSH » n’est pas disponible**, changez le nom (par exemple, votrenomADFTutorialDataFactoryPSH). Utilisez ce nom à la place d’ADFTutorialFactoryPSH quand vous effectuez les étapes de ce didacticiel.

## <a name="CreateLinkedServices"></a>Étape 2 : créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un stockage Azure, une base de données SQL Azure ou une base de données SQL Server locale qui contient des données d’entrée ou qui stocke les données de sortie d’un pipeline Data Factory. Un service de calcul est un service qui traite les données d’entrée et génère des données de sortie.

Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. Le service lié StorageLinkedService relie un compte de stockage Azure Storage et AzureSqlLinkedService relie une base de données Azure SQL à la fabrique de données : **ADFTutorialDataFactoryPSH**. Vous allez créer un pipeline plus loin dans ce didacticiel pour copier les données d’un conteneur d’objets blob dans StorageLinkedService vers une table SQL dans AzureSqlLinkedService.

### Créer un service lié pour un compte de stockage Azure
1.	Créez un fichier JSON nommé **StorageLinkedService.json** dans **C:\\ADFGetStartedPSH** avec le contenu suivant. Créez le dossier ADFGetStartedPSH s’il n’existe pas déjà.

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		  }
		}

	Remplacez **accountname** par le nom de votre compte de stockage Azure et **accountkey** par sa clé.
2.	Dans **Azure PowerShell**, basculez vers le dossier **ADFGetStartedPSH**. 
3.	Vous pouvez utiliser l’applet de commande **New-AzureDataFactoryLinkedService** pour créer un service lié. Cette applet de commande et d’autres applets de commande Data Factory que vous utilisez dans ce didacticiel vous obligent à transmettre des valeurs aux paramètres **ResourceGroupName** et **DataFactoryName**. Vous pouvez également utiliser **Get-AzureDataFactory** pour obtenir un objet DataFactory et transmettre l’objet sans avoir à taper ResourceGroupName et DataFactoryName chaque fois que vous exécutez une applet de commande. Exécutez la commande suivante pour affecter le résultat de l’applet de commande **Get-AzureDataFactory** à une variable : **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	À présent, exécutez l’applet de commande **New-AzureDataFactoryLinkedService** pour créer le service lié : **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si vous n’avez pas exécuté l’applet de commande **Get-AzureDataFactory** et affecté la sortie à la variable **$df**, il vous faut spécifier des valeurs pour les paramètres ResourceGroupName et DataFactoryName comme suit.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Si vous fermez Azure PowerShell au milieu du didacticiel, il vous faut exécuter l’applet de commande Get-AzureDataFactory au prochain lancement d’Azure PowerShell pour terminer le didacticiel.

### Créer un service lié pour une base de données SQL Azure
1.	Créez un fichier JSON nommé AzureSqlLinkedService.json avec le contenu suivant.

		{
		  "name": "AzureSqlLinkedService",
		  "properties": {
		    "type": "AzureSqlDatabase",
		    "typeProperties": {
		      "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		  }
		}

	Remplacez **servername**, **databasename**, **username@servername** et **password** par les noms de votre serveur SQL Azure, de la base de données, du compte d'utilisateur et par le mot de passe.

2.	Exécutez la commande suivante pour créer un service lié :
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé sur votre serveur SQL Azure. Pour vérifier et l’activer, procédez comme suit :

	1. Cliquez sur le hub **PARCOURIR** situé à gauche, puis sur **Serveurs SQL**.
	2. Sélectionnez votre serveur, puis cliquez sur **PARAMÈTRES** dans le panneau SQL SERVER.
	3. Dans le panneau **PARAMÈTRES**, cliquez sur **Pare-feu**.
	4. Dans le panneau **Paramètres de pare-feu**, cliquez sur **Activer** pour **Autoriser l’accès aux services Azure**.
	5. Cliquez sur le concentrateur **ACTIVE** à gauche pour basculer vers le panneau **Data Factory** où vous étiez.
	

## <a name="CreateInputAndOutputDataSets"></a>Étape 3 : créer des tables d’entrée et de sortie

À l’étape précédente, vous avez créé les services liés **StorageLinkedService** et **AzureSqlLinkedService** pour lier un compte de stockage Azure et la base de données SQL Azure à la fabrique de données : **ADFTutorialDataFactoryPSH**. Dans cette étape, vous allez créer des tables qui représentent des données d’entrée et de sortie pour l’activité de copie dans le pipeline, que vous allez créer à l’étape suivante.

Une table est un jeu de données rectangulaire et il s'agit du seul type de jeu de données qui est pris en charge pour l'instant. La table d'entrée dans ce didacticiel fait référence à un conteneur d'objets blob dans le stockage Azure Storage vers lequel pointe StorageLinkedService, et la table de sortie fait référence à une table SQL dans la base de données SQL Azure vers laquelle pointe AzureSqlLinkedService.

### Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
Ignorez cette étape si vous avez suivi le didacticiel de l’article [Prise en main d’Azure Data Factory][adf-get-started].

Vous devez effectuer les étapes suivantes pour préparer le stockage d’objets blob Azure et la base de données SQL Azure pour ce didacticiel.
 
* Créez un conteneur d’objets blob nommé **adftutorial** dans le stockage d’objets blob Azure vers lequel pointe **StorageLinkedService**. 
* Créez et téléchargez un fichier texte, **emp.txt**, en tant qu’objet blob vers le conteneur **adftutorial**. 
* Créez une table nommée **emp** dans la base de données SQL Azure vers laquelle pointe **AzureSqlLinkedService**.


1. Lancez le Bloc-notes, collez le texte suivant, puis enregistrez-le sous le nom **emp.txt** dans le dossier **C:\\ADFGetStartedPSH** sur votre disque dur. 

        John, Doe
		Jane, Doe
				
2. Utilisez des outils tels que l’[Explorateur Azure Storage](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et télécharger le fichier **emp.txt** vers ce dernier.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Si vous avez installé SQL Server 2014 sur votre ordinateur : suivez les instructions de l’article [Étape 2 : se connecter à la base de données SQL de la gestion de base de données SQL à l’aide de SQL Server Management Studio][sql-management-studio] pour vous connecter à votre serveur SQL Azure et exécuter le script SQL.

	Si Visual Studio 2013 est installé sur votre ordinateur : dans le portail Azure en version préliminaire ([http://portal.azure.com](http://portal.sazure.com)), cliquez sur le concentrateur **PARCOURIR** sur la gauche, cliquez sur **Serveurs SQL**, sélectionnez votre base de données, puis cliquez sur **Ouvrir dans Visual Studio** dans la barre d’outils pour vous connecter à votre serveur SQL Azure et exécuter le script. Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure afin d’autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez l’article ci-dessus pour savoir comment configurer le pare-feu pour votre serveur SQL Azure.
		
### Créer une table d'entrée 
Une table est un jeu de données rectangulaire qui dispose d'un schéma. Dans cette étape, vous allez créer une table nommée **EmpBlobTable** qui pointe vers un conteneur d’objets blob dans l’emplacement Azure Storage représenté par le service lié **StorageLinkedService**. Ce conteneur d’objets blob (\*\*adftutorial\*\*) contient les données d’entrée dans le fichier : **emp.txt**.

1.	Créez un fichier JSON nommé **EmpBlobTable.json** dans le dossier **C:\\ADFGetStartedPSH** avec le contenu suivant :

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
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
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
	
	- Le **type** d’emplacement est défini sur **AzureBlob**.
	- **linkedServiceName** a la valeur **StorageLinkedService**. 
	- **folderPath** est défini sur le conteneur **adftutorial**. Vous pouvez également spécifier le nom d'un objet blob dans le dossier. Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.  
	- Le **type** de format a la valeur **TextFormat**.
	- Il existe deux champs dans le fichier texte, **FirstName** et **LastName**, séparés par une virgule (\*\*columnDelimiter\*\*).	
	- **availability** est défini sur **hourly** (\*\*frequency\*\* a la valeur **hour** et **interval** est défini sur **1** ), de sorte que le service Data Factory recherche les données d'entrée toutes les heures dans le dossier racine du conteneur d'objets blob (\*\*adftutorial\*\*) que vous avez spécifié.

	Si vous ne spécifiez pas de nom (**fileName**) pour une **table** **d'entrée**, tous les fichiers/objets blob du dossier d'entrée (\*\*folderPath\*\*) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour plus d’informations.
 
	Si vous ne spécifiez pas de nom (**fileName**) pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

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

	Pour plus d’informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2.	Exécutez la commande suivante pour créer la table Data Factory :

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Créer la table de sortie
Dans cette partie de l’étape, vous allez créer une table de sortie nommée **EmpSQLTable** qui pointe vers une table SQL (\*\*emp\*\*) de la base de données SQL Azure, représentée par le service lié **AzureSqlLinkedService**. Le pipeline copie les données à partir de l’objet blob d’entrée vers la table **emp**.

1.	Créez un fichier JSON nommé **EmpSQLTable.json** dans le dossier **C:\\ADFGetStartedPSH** avec le contenu suivant.
		
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
		    "linkedServiceName": "AzureSqlLinkedService1",
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
	
	* Le **type** d’emplacement est défini sur **AzureSqlTable**.
	* **linkedServiceName** est défini sur **AzureSqlLinkedService**.
	* **tablename** est défini sur **emp**.
	* Il existe trois colonnes (**ID**, **FirstName** et **LastName**) dans la table emp de la base de données. Toutefois, ID étant une colonne d'identité, vous devez donc spécifier uniquement **FirstName** et **LastName**.
	* **availability** est défini sur **hourly** (\*\*frequency\*\* a la valeur **hour** et **interval** a la valeur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Azure.

2.	Exécutez la commande suivante pour créer la table Data Factory :
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Étape 4 : créer et exécuter un pipeline
Dans cette étape, vous créez un pipeline avec une **activité de copie** qui utilise **EmpTableFromBlob** en entrée et **EmpSQLTable** en sortie.

1.	Créez un fichier JSON nommé **ADFTutorialPipeline.json** dans le dossier **C:\\ADFGetStartedPSH** avec le contenu suivant : 
	
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
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

	Notez les points suivants :

	- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **Copy**.
	- L'entrée de l'activité a la valeur **EmpTableFromBlob** et la sortie de l'activité a la valeur **EmpSQLTable**.
	- Dans la section **transformation**, **BlobSource** est spécifié en tant que type de source et **SqlSink** est spécifié en tant que type de récepteur.

	Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end** par le jour suivant. Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.
	
	Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « \*\*start + 48 heures\*\* ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour la propriété **end**.
	
	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
	Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2.	Exécutez la commande suivante pour créer la table Data Factory : 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

## <a name="MonitorDataSetsAndPipeline"></a>Étape 5 : surveiller les jeux de données et le pipeline
Au cours de cette étape, vous allez utiliser Azure PowerShell pour surveiller ce qui se passe dans une fabrique de données Azure.

1.	Exécutez **Get-AzureDataFactory** et affectez le résultat à une variable $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Exécutez **Get-AzureDataFactorySlice** pour obtenir des détails sur toutes les tranches de **EmpSQLTable**, qui est la table de sortie du pipeline.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Remplacez les parties correspondant à l’année, au mois et au jour dans le paramètre **StartDateTime** par l’année, le mois et le jour en cours. Cela doit correspondre à la valeur **Start** dans le pipeline JSON.

	Vous devez voir 24 tranches, une pour chaque heure entre 12: 00 du jour actuel et 12: 00 le lendemain.
	
	**Première tranche :**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Dernière tranche :**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Exécutez **Get-AzureDataFactoryRun** pour obtenir les détails d’exécution d’activité pour une tranche **spécifique**. Modifiez la valeur du paramètre **StartDateTime** pour qu’elle corresponde à l’heure **Start** de la tranche dans la sortie ci-dessus. La valeur de **StartDateTime** doit être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-03-03T22:00:00Z.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	Le résultat ressemble à ce qui suit :

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

Consultez la [référence des applets de commande Data Factory][cmdlet-reference] pour obtenir une documentation complète sur les applets de commande Data Factory.

## Étapes suivantes

Article | Description
------ | ---------------
[Copier des données avec Azure Data Factory (activité de copie)][copy-activity] | Cet article fournit une description détaillée de l'**activité de copie** que vous avez utilisée dans ce didacticiel. 
[Permettre à vos pipelines d'utiliser des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas permettant de copier les données d’une **base de données SQL Server locale** vers un objet blob Azure. 
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas permettant d’exécuter un script **Hive/Pig** à l’aide de **HDInsight Activity** pour traiter les données d’entrée afin de produire des données de sortie.
[Didacticiel : déplacement et traitement de fichiers journaux à l’aide de Data Factory][adf-tutorial] | Cet article comporte une **procédure pas à pas de bout en bout** qui montre comment implémenter un **scénario réel** à l’aide d’Azure Data Factory pour transformer des données de fichiers journaux en informations pertinentes.
[Utilisation d’activités personnalisées dans une fabrique de données][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une **activité personnalisée** et de l’utiliser dans un pipeline. 
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment **résoudre des problèmes** liés à Azure Data Factory. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence sur les applets de commande Azure Data Factory][cmdlet-reference] | Ces informations de référence comportent des détails sur toutes les **applets de commande Data Factory**.
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence pour les développeurs comportent des informations complètes sur les applets de commande, le script JSON, les fonctions, etc. 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2
 

<!---HONumber=July15_HO5-->